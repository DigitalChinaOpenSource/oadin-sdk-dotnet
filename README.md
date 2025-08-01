# OadinClient (.NET SDK)

[English] | [简体中文](./README_zhCN.md)

---

## ✨ Overview
**OadinClient** is the official .NET SDK for the Oadin platform.  
This README shows how to add the package from a local NuGet source and includes **all** API examples without any modification.

---

## 📦 Local Source Setup
```bash
dotnet pack --configuration Release  

mkdir local-nuget

cp ./bin/Release/OadinClient.1.0.0.nupkg ./local-nuget

# Add this directory to your NuGet sources
# Check sources: dotnet nuget list source
# Afterwards, any project can reference via --source LocalOadin
dotnet nuget add source ./local-nuget --name LocalOadin

dotnet add package OadinClient --version 1.0.0 --source LocalOadin
dotnet add package OadinClient --version 1.0.0 --source .
```

---

## 🧑‍💻 Complete API Demo
```csharp
using OadinClient;

var client = new OadinClient();


// Get service list
var services = await client.GetServicesAsync();
Console.WriteLiine(services);

// Create a service
var requestData = new
{
    service_name = "chat/embed/generate/text-to-image",
    service_source = "remote/local",
    service_provider_name = "local_ollama_chat",
    api_flavor = "ollama/openai/...",
    auth_type = "none/apikey/token/credentials",
    method = "GET/POST",
    desc = "服务描述",
    url = "",
    auth_key = "your_api_key",
    skip_model = false,
    model_name = "llama2",
};
var result = await client.InstallServiceAsync(requestData);
Console.WriteLine(result);

// Update a service
var requestData = new
{
    service_name = "chat/embed/generate/text-to-image",
    hybrid_policy = "default/always_local/always_remote",
    remote_provider = "remote_openai_chat",
    local_provider = "local_ollama_chat"
};
var result = await client.UpdateServiceAsync(requestData);
Console.WriteLine(result);

// Get model list
var models = await client.GetModelsAsync();
Console.WriteLine(models);

// Download a model
var requestData = new
{
    model_name = "llama2",
    service_name = "chat/embed/generate/text-to-image",
    service_source = "remote/local",
    provider_name = "local_ollama_chat/remote_openai_chat/..."
};
var result = await client.InstallModelAsync(requestData);
Console.WriteLine(result);

// Download a model (streaming)
var requestData = new
{
    model_name = "nomic-embed-text",
    service_name = "embed",
    service_source = "local",
    provider_name = "local_ollama_embed"
};
await client.InstallModelStreamAsync(
    requestData,
    onData: (json) => Console.WriteLine("Stream: " + json),
    onError: (error) => Console.WriteLine("Error: " + error),
    onEnd: () => Console.WriteLine("Stream install complete")
);

// Cancel streaming model download
var requestData = new
{
    model_name = "nomic-embed-text"
};
await client.CancelInstallModelAsync(requestData);

// Uninstall a model
var requestData = new
{
    model_name = "llama2",
    service_name = "chat/embed/generate/text-to-image",
    service_source = "remote/local",
    provider_name = "local_ollama_chat/remote_openai_chat/..."
};
var result = await client.DeleteModelAsync(requestData);
Console.WriteLine(result);

// Get service providers
var serviceProviders = await client.GetServiceProvidersAsync();
Console.WriteLine(serviceProviders);

// Add a model provider
var requestData = new
{
    service_name = "chat/embed/generate/text-to-image",
    service_source = "remote/local",
    flavor_name = "ollama/openai/...",
    provider_name = "local_ollama_chat/remote_openai_chat/...",
    desc = "提供商描述",
    method = "GET/POST",
    url = "https://api.example.com",
    auth_type = "none/apikey/token/credentials",
    auth_key = "your_api_key",
    models = new[] { "qwen2:7b", "deepseek-r1:7b" },
    extra_headers = new { },
    extra_json_body = new {  },
    properties = new { }
};
var result = await client.AddServiceProviderAsync(requestData);
Console.WriteLine(result);

// Update a model provider
var requestData = new
{
    service_name = "chat/embed/generate    text-to-image",
    service_source = "remote/local",
    flavor_name = "ollama/openai/...",
    provider_name = "local_ollama_chat/remote_openai_chat/...",
    desc = "更新后的描述",
    method = "GET/POST",
    url = "https://api.example.com",
    auth_type = "none/apikey/token/credentials",
    auth_key = "your_api_key",
    models = new[] { "qwen2:7b", "deepseek-r1:7b" },
    extra_headers = new { },
    extra_json_body = new {  },
    properties = new { }
};
var result = await client.UpdateServiceProviderAsync(requestData);

// Delete a model provider
var requestData = new
{
    provider_name = "local_ollama_chat/remote_openai_chat/..."
};
var result = await client.DeleteServiceProviderAsync(requestData);

// Get model list (from engine)
var models = await client.GetModelAvailiableAsync();
Console.WriteLine(models);

// Get recommended model list
var models = await client.GetModelRecommendedAsync();
Console.WriteLine(models);

// Get supported model list
var models = await client.GetModelSupportedAsync();
Console.WriteLine(models);

// Get model list from Wenxue platform
var requestData = new
{
    env_type = "dev/product",
};
var models = await client.GetModelListAsync(requestData);
Console.WriteLine(models);

// Import config file
var result = await client.ImportConfigAsync("path/to/.oadin");
Console.WriteLine(result);

// Export config file
var result = await client.ExportConfigAsync();
Console.WriteLine(result);

// Streaming Chat
var requestData = new { 
    model = "deepseek-r1:7b", 
    stream = true,
    messages = new[] { 
        new { role = "user", content = "Who are you?" } 
    }
};
await client.ChatAsync(
    requestData,
    isStream: true,
    onData: (data) => Console.WriteLine("Stream: " + data),
    onError: (error) => Console.WriteLine("Error: " + error),
    onEnd: () => Console.WriteLine("Stream end")
);

// Non-streaming Chat
var requestData = new { 
    model = "deepseek-r1:7b", 
    stream = false,
    messages = new[] { 
        new { role = "user", content = "Who are you?" } 
    }
};
var result = await client.ChatAsync(requestData);
Console.WriteLine(result);

// Embed
var requestData = new { 
    model = "nomic-embed-text",
    imput = new[] { 
        "Foo", 
        "Bar" 
    },
};
var result = await client.EmbedAsync(requestData);
Console.WriteLine(result);

// Text-to-image
var requestData = new { 
    model = "wanx2.1-t2i-turbo",
    prompt = "A beautiful flower shop with wooden doors"
};
var result = await client.TextToImageAsync(requestData);
Console.WriteLine(result);

```
