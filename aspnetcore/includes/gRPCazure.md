## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC nie jest obsługiwane w Azure App Service

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) nie jest obecnie obsługiwana w Azure App Service lub IIS. Implementacja http/2 protokołu HTTP. sys nie obsługuje nagłówków końcowych odpowiedzi HTTP, które gRPC opierają się na. Aby uzyskać więcej informacji, zobacz [problem w usłudze GitHub](https://github.com/aspnet/AspNetCore/issues/9020).
