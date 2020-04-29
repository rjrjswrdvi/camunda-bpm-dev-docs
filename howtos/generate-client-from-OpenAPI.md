# How to generate a client from the OpenAPI documentation of Camunda BPM REST

1. Download the [openapi-generator-cli](https://github.com/OpenAPITools/openapi-generator)
2. Get the openapi.json file that you want to generate the client with
3. Pick a language of the supported: [link](https://github.com/OpenAPITools/openapi-generator#overview)
4. Execute the following command:
  * general example
```
java -jar openapi-generator-cli-VERSION.jar generate -i $PATH_TO_OPENAPI/openapi.json -g LANGUAGE -o OUTPUT_DIR
```
  * Java client example
```
java -jar openapi-generator-cli-4.2.3.jar generate -i ./openapi.json -g java -o ./java-clien-output
```

## More:
### List of other client generators:
  * [Autorest](https://github.com/Azure/autorest#generators) - open source tool managed by microsoft

`autorest --v3 --csharp --input-file=./openapi.json`

  * [Swagger-Codegen](https://github.com/swagger-api/swagger-codegen#overview)

`java -jar swagger-codegen-cli-3.0.19.jar generate -i ./openapi.json -l csharp -c ./config.json`

  * [NSwag](https://github.com/RicoSuter/NSwag): The Swagger/OpenAPI toolchain for .NET, ASP.NET Core and TypeScript - community development
