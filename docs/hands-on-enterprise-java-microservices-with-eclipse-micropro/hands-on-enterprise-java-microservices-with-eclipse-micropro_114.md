# 使用示例

以下是一些 MicroProfile OpenAPI 注解的使用示例：

示例 1 – 简单的操作描述（缩写）：

```
@GET
@Path("/findByMake")
@Operation(summary = "Finds cars by make",
           description = "Find cars by their manufacturer")
public Response findCarsByMake(...)
{ ... }
```

以下是示例 1 的输出：

```
/car/findByMake:
  get:
    summary: Finds cars by make
    description: Find cars by their manufacturer
```

示例 2 – 具有不同响应的操作（缩写）：

```
@GET
@Path("/{name}")
@Operation(summary = "Get customer by name")
  @APIResponse(description = "The customer",
             content = @Content(mediaType = "application/json",
                                schema = @Schema(implementation = Customer.class))),
@APIResponse(responseCode = "400", description = "Customer not found")
public Response getCustomerByName(
        @Parameter(description = "The name of the customer to be fetched", required = true) @PathParam("name") String name)

{...}
```

以下是示例 2 的输出：

```
/customer/{name}:
    get:
       summary: Get customer by name
       operationId: getCutomerByName
       parameters:
       - name: name
         in: path
         description: 'The name of the customer to be fetched'
         required: true
         schema:
           type: string
       responses:
         default:
           description: The customer
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/Customer'
         400:
           description: Customer not found
```

更多示例，请参考 MicroProfile OpenAPI 规范维基页面：[`github.com/eclipse/microprofile-open-api/wiki`](https://github.com/eclipse/microprofile-open-api/wiki)。



