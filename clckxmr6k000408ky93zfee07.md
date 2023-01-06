# Why I make my own code generator?

The architecture of the [Encyclopedia Galactica](https://github.com/EncyclopediaGalactica) platform, we can say that it is a platform, is a microservices architecture. Or something like that -ish...

![](https://media.giphy.com/media/26gs8GymfRPhduyyY/giphy-downsized-large.gif align="center")

A single service's source code looks like the screenshot below.

In short, a crapload of projects. Even if I put interfaces, exceptions and implementations in a single project I'll have 5-10 with a nice amount of boilerplate. For example:

* dtos
    
* dto tests
    
* client
    
* client tests
    
* client models
    
* client model tests
    
* controllers
    

All of the above is just boilerplate and can be generated from the information available in the endpoint, implemented by a single service, [OpenApi](https://swagger.io/specification/) specifications.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1673030682820/a6611d80-fda6-4130-89af-3845ce8c6773.png align="center")

# But, why I do make one?

The one I know, [openapi-generator](https://github.com/OpenAPITools/openapi-generator), cannot do the Dto and controller part and I don't like the output. I worked with its fork previously and I didn't like it.

I make one because it is an excellent opportunity for extra experience. Transforming the information from an OpenApi file to another format and structure which eventually will be generated to source code requires:

* a scheduling problem,
    
* a lot of work with data structures,
    
* a decent understanding of the generated language syntax,
    
* a decent amount of design decisions,
    
* crazy amount of testing.
    

The third reason is that it will be written in C# and not java.

![](https://media.giphy.com/media/CuuSHzuc0O166MRfjt/giphy.gif align="center")

The fourth reason is that I can say: "I wrote a source code generator.". It sounds way better than "I implemented a controller."

# What will be generated?

I'm going to generate a client library, an sdk, that makes it possible to communicate with the Rest endpoint comfortably. The idea is that the application, a Blazor UI, using the Sdk has to populate an object (a type-specific [RequestModel](https://github.com/EncyclopediaGalactica/Sdk.Core/blob/main/Model.Interfaces/IRequestModel.cs)) with the necessary data, pass it to the Sdk and call the necessary method to fire the communication with the endpoint. The result is a type specific [ResponseModel](https://github.com/EncyclopediaGalactica/Sdk.Core/blob/main/Model.Interfaces/IHttpResponseModel.cs). We used a [similar pattern at IBM](https://github.com/IBM/platform-services-java-sdk/blob/main/modules/catalog-management/src/test/java/com/ibm/cloud/platform_services/catalog_management/v1/CatalogManagementIT.java), the idea comes from there.

The Document Service E2E tests are good examples.

```csharp
    [Fact]
    public async Task Return_201_WhenCreatingNewSourceFormatNode_AndReturnResult()
    {
        // Arrange
        string name = "asd";
        SourceFormatNodeAddRequestModel requestModel = new SourceFormatNodeAddRequestModel.Builder()
            .SetName(name)
            .Build();

        // Act
        SourceFormatNodeAddResponseModel responseModel = await SourceFormatsSdk.SourceFormatNode.AddAsync(requestModel)
            .ConfigureAwait(false);

        // Assert
        responseModel.Should().NotBeNull();
        responseModel.IsOperationSuccessful.Should().BeTrue();
        responseModel.Result.Should().NotBeNull();
        responseModel.Result.Should().BeOfType<SourceFormatNodeDto>();
        responseModel.Result.Id.Should().BeGreaterThan(0);
        responseModel.Result.Name.Should().Be(name);
    }
```

## Data Transfer Objects

The Dtos are the contract between the endpoint and its consumers. These are described in the OpenApi specification, so they can be generated.

## Data Transfer Object tests

I'm one of those who believe in tests against plain objects. If anything changes I want to know about it.

## Controllers

This information is also available in the OpenApi file, so it will be generated. I tend to [put each controller method in its own file](https://github.com/EncyclopediaGalactica/DocumentService/tree/main/Controllers/SourceFormatNode).

## Sdk Client for the endpoint

The supported operations are described in the OpenApi specification and the endpoint-specific part of the Sdk Client can be generated. The common part of the Http communication with the endpoint can be generalized and put in an [Sdk Core](https://github.com/EncyclopediaGalactica/Sdk.Core) library and distributed as [Nuget package](https://github.com/orgs/EncyclopediaGalactica/packages).

## Sdk Client for the endpoint tests

These unit and integration tests can be generated.

## Sdk Models

Every operation has its own request and response model. Dead boring boilerplate and must be generated.

## Sdk Models' unit tests

Way more dead boring boilerplate than the previous one. However, these are important tests. But, they can be generated.

# What the code generator doesn't do?

The OpenApi specification offers some magic... Some absolute magic...

![](https://media.giphy.com/media/Ij3TJHnhJjXEc/giphy.gif align="center")

The keywords ["oneOf", "anyOf" and "allOf"](https://swagger.io/docs/specification/data-models/oneof-anyof-allof-not/) are gates to a specific level of hell. These are the keywords I'm not going to put into my OpenApi specification files. And when I have to I'll put the task aside for a few days and reconsider what I want to do. The generator will not cover these cases. This is one of the limitations.

The second limitation is that it will generate C# code only. I do not have any plan to call the system from any other language.

![](https://media.giphy.com/media/DBQUBwbv8hbwY/giphy.gif align="center")