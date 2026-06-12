using System.Text;
using System.Text.Json;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Читаем API ключ из переменных среды
string? apiKey = Environment.GetEnvironmentVariable("DEEPSEEK_KEY");
var httpClient = new HttpClient();

app.MapPost("/ask", async (HttpContext context) =>
{
    // 1. Получаем вопрос из Roblox
    using var reader = new StreamReader(context.Request.Body);
    var body = await reader.ReadToEndAsync();
    var robloxData = JsonSerializer.Deserialize<JsonElement>(body);
    string question = robloxData.GetProperty("question").GetString() ?? "";

    // 2. Готовим запрос для Deepseek
    var deepseekRequest = new
    {
        model = "deepseek-chat",
        messages = new[] { new { role = "user", content = question } }
    };

    var requestMessage = new HttpRequestMessage(HttpMethod.Post, "https://api.deepseek.com/v1/chat/completions");
    requestMessage.Headers.Add("Authorization", $"Bearer {apiKey}");
    requestMessage.Content = new StringContent(JsonSerializer.Serialize(deepseekRequest), Encoding.UTF8, "application/json");

    // 3. Отправляем и получаем ответ
    var response = await httpClient.SendAsync(requestMessage);
    var result = await response.Content.ReadAsStringAsync();

    // 4. Отправляем результат обратно в Roblox
    return Results.Content(result, "application/json");
});

// Слушаем порт, который даст Render
var port = Environment.GetEnvironmentVariable("PORT") ?? "8080";
app.Run($"http://0.0.0.0:{port}");