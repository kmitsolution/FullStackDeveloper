## Student.cs
```
using System;
using System.Data;
using System.Threading.Tasks;
using Microsoft.Data.SqlClient;

public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime DOB { get; set; }

    // Inserts this student into the database and sets the generated Id.
    public async Task<int> InsertAsync(string connectionString)
    {
        if (string.IsNullOrWhiteSpace(Name)) throw new ArgumentException("Name is required", nameof(Name));

        using (var cn = new SqlConnection(connectionString))
        using (var cmd = cn.CreateCommand())
        {
            cmd.CommandText = "INSERT INTO student (Name, DOB) VALUES (@name, @dob); SELECT SCOPE_IDENTITY();";
            cmd.Parameters.AddWithValue("@name", (object)Name ?? DBNull.Value);
            cmd.Parameters.AddWithValue("@dob", DOB);

            await cn.OpenAsync();
            var result = await cmd.ExecuteScalarAsync();
            if (result == null || result == DBNull.Value) return 0;

            try
            {
                switch (result)
                {
                    case int i:
                        Id = i;
                        return i;
                    case long l:
                        Id = (int)l;
                        return Id;
                    case decimal d:
                        Id = Convert.ToInt32(d);
                        return Id;
                    default:
                        Id = Convert.ToInt32(result);
                        return Id;
                }
            }
            catch
            {
                return 0;
            }
        }
    }

    public async Task<bool> UpdateAsync(string connectionString)
    {
        if (Id <= 0) throw new ArgumentException("Id must be set for update", nameof(Id));

        using (var cn = new SqlConnection(connectionString))
        using (var cmd = cn.CreateCommand())
        {
            cmd.CommandText = "UPDATE student SET Name = @name, DOB = @dob WHERE id = @id";
            cmd.Parameters.AddWithValue("@name", (object)Name ?? DBNull.Value);
            cmd.Parameters.AddWithValue("@dob", DOB);
            cmd.Parameters.AddWithValue("@id", Id);

            await cn.OpenAsync();
            var rows = await cmd.ExecuteNonQueryAsync();
            return rows > 0;
        }
    }

    public async Task<bool> DeleteAsync(string connectionString)
    {
        if (Id <= 0) throw new ArgumentException("Id must be set for delete", nameof(Id));

        using (var cn = new SqlConnection(connectionString))
        using (var cmd = cn.CreateCommand())
        {
            cmd.CommandText = "DELETE FROM student WHERE id = @id";
            cmd.Parameters.AddWithValue("@id", Id);

            await cn.OpenAsync();
            var rows = await cmd.ExecuteNonQueryAsync();
            return rows > 0;
        }
    }

    // Patch: update only provided fields (name and/or dob)
    public static async Task<bool> PatchAsync(int id, string name, DateTime? dob, string connectionString)
    {
        if (id <= 0) throw new ArgumentException("id is required", nameof(id));
        if (name == null && dob == null) throw new ArgumentException("At least one field must be provided to patch");

        using (var cn = new SqlConnection(connectionString))
        using (var cmd = cn.CreateCommand())
        {
            var sets = new System.Text.StringBuilder();
            if (name != null)
            {
                sets.Append("Name = @name");
                cmd.Parameters.AddWithValue("@name", (object)name ?? DBNull.Value);
            }
            if (dob != null)
            {
                if (sets.Length > 0) sets.Append(", ");
                sets.Append("DOB = @dob");
                cmd.Parameters.AddWithValue("@dob", dob.Value);
            }

            cmd.CommandText = $"UPDATE student SET {sets} WHERE id = @id";
            cmd.Parameters.AddWithValue("@id", id);

            await cn.OpenAsync();
            var rows = await cmd.ExecuteNonQueryAsync();
            return rows > 0;
        }
    }
}

```

## Program.cs
```
using Microsoft.Data.SqlClient;
using System.Text.Json;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();
app.MapGet("/", () => "Home Page");

app.MapGet("/student", async (HttpRequest request) =>
{
    string id = Convert.ToString(request.Query["id"]);
    if (string.IsNullOrWhiteSpace(id))
    {
        return Results.BadRequest(new { error = "id query parameter is required" });
    }

    if (!int.TryParse(id, out int idVal))
    {
        return Results.BadRequest(new { error = "id must be an integer" });
    }

    using (SqlConnection cn = new SqlConnection("Server=localhost;Database=DotNetDB;Trusted_Connection=True;TrustServerCertificate=True;"))
    using (SqlCommand cmd = cn.CreateCommand())
    {
        cmd.CommandText = "SELECT id, Name, DOB FROM student WHERE id = @id";
        cmd.Parameters.AddWithValue("@id", idVal);
        await cn.OpenAsync();
        using (SqlDataReader reader = await cmd.ExecuteReaderAsync())
        {
            if (await reader.ReadAsync())
            {
                var student = new Student
                {
                    Id = reader.GetInt32(0),
                    Name = reader.IsDBNull(1) ? null : reader.GetString(1),
                    DOB = reader.IsDBNull(2) ? DateTime.MinValue : reader.GetDateTime(2)
                };

                return Results.Ok(student);
            }
        }
    }

    return Results.NotFound();

});

// POST to create a new student using Student.InsertAsync
app.MapPost("/student", async (Student student) =>
{
    if (student == null || string.IsNullOrWhiteSpace(student.Name))
    {
        return Results.BadRequest(new { error = "Student Name is required" });
    }

    var connectionString = "Server=localhost;Database=DotNetDB;Trusted_Connection=True;TrustServerCertificate=True;";

    try
    {
        var newId = await student.InsertAsync(connectionString);
        if (newId > 0)
        {
            return Results.Created($"/student?id={newId}", student);
        }

        return Results.StatusCode(500);
    }
    catch (ArgumentException ae)
    {
        return Results.BadRequest(new { error = ae.Message });
    }
    catch (Exception ex)
    {
        return Results.Problem(detail: ex.Message, statusCode: 500);
    }
});

// PUT to update entire student (requires Id)
app.MapPut("/student", async (Student student) =>
{
    if (student == null || student.Id <= 0) return Results.BadRequest(new { error = "Student Id is required for update" });

    var connectionString = "Server=localhost;Database=DotNetDB;Trusted_Connection=True;TrustServerCertificate=True;";
    try
    {
        var updated = await student.UpdateAsync(connectionString);
        return updated ? Results.NoContent() : Results.NotFound();
    }
    catch (ArgumentException ae)
    {
        return Results.BadRequest(new { error = ae.Message });
    }
    catch (Exception ex)
    {
        return Results.Problem(detail: ex.Message, statusCode: 500);
    }
});

// DELETE by id (query param)
app.MapDelete("/student", async (HttpRequest request) =>
{
    string id = Convert.ToString(request.Query["id"]);
    if (string.IsNullOrWhiteSpace(id)) return Results.BadRequest(new { error = "id query parameter is required" });
    if (!int.TryParse(id, out int idVal)) return Results.BadRequest(new { error = "id must be an integer" });

    var connectionString = "Server=localhost;Database=DotNetDB;Trusted_Connection=True;TrustServerCertificate=True;";
    var student = new Student { Id = idVal };
    try
    {
        var deleted = await student.DeleteAsync(connectionString);
        return deleted ? Results.NoContent() : Results.NotFound();
    }
    catch (ArgumentException ae)
    {
        return Results.BadRequest(new { error = ae.Message });
    }
    catch (Exception ex)
    {
        return Results.Problem(detail: ex.Message, statusCode: 500);
    }
});

// PATCH to update partial fields (id query + JSON body with name and/or dob)
app.MapMethods("/student", new[] { "PATCH" }, async (HttpRequest request) =>
{
    string id = Convert.ToString(request.Query["id"]);
    if (string.IsNullOrWhiteSpace(id)) return Results.BadRequest(new { error = "id query parameter is required" });
    if (!int.TryParse(id, out int idVal)) return Results.BadRequest(new { error = "id must be an integer" });

    using var doc = await JsonDocument.ParseAsync(request.Body);
    var root = doc.RootElement;
    string name = null;
    DateTime? dob = null;

    if (root.TryGetProperty("name", out var nameProp) && nameProp.ValueKind == JsonValueKind.String)
    {
        name = nameProp.GetString();
    }

    if (root.TryGetProperty("dob", out var dobProp))
    {
        if (dobProp.ValueKind == JsonValueKind.String)
        {
            if (DateTime.TryParse(dobProp.GetString(), out var d)) dob = d;
        }
        else if (dobProp.ValueKind == JsonValueKind.Number)
        {
            if (dobProp.TryGetInt64(out var unix)) dob = DateTimeOffset.FromUnixTimeSeconds(unix).DateTime;
        }
    }

    if (name == null && dob == null) return Results.BadRequest(new { error = "At least one of name or dob must be provided" });

    var connectionString = "Server=localhost;Database=DotNetDB;Trusted_Connection=True;TrustServerCertificate=True;";
    try
    {
        var patched = await Student.PatchAsync(idVal, name, dob, connectionString);
        return patched ? Results.NoContent() : Results.NotFound();
    }
    catch (ArgumentException ae)
    {
        return Results.BadRequest(new { error = ae.Message });
    }
    catch (Exception ex)
    {
        return Results.Problem(detail: ex.Message, statusCode: 500);
    }
});

app.Run();

```
