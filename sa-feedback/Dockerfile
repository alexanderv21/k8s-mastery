FROM microsoft/dotnet:2.1.500-sdk-alpine AS builder

COPY . ./app
WORKDIR /app

RUN dotnet publish -c Release -o out

FROM microsoft/dotnet:2.1.5-aspnetcore-runtime-alpine AS runtime

COPY --from=builder /app/out ./app
WORKDIR /app

ENTRYPOINT ["dotnet", "SA.Feedback.dll"]
