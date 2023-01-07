# Code generator: DTO phase, status

> This article is part of a series about the code generator. Other posts in this series can be found here!

Let's see where are with this code generator thingy.

![](https://media.giphy.com/media/zl3b8VDACq9aw/giphy.gif align="center")

I split the code generation into different phases where the first one is generating the Dto classes based on the OpenApi yaml file. The second one is generating test cases for the Dto classes.

The following sections discuss what the code generator can do at the moment.

## Configuration file

The code generator is configured by a configuration file in json format. I chose this because the [command line](https://github.com/dotnet/command-line-api) api can accept a maximum of up to 8 parameters. It is a bonker..., but [increasing the parameters is already requested](https://github.com/dotnet/command-line-api/issues/2000).

From the command line, the generator requires only the path of the configuration file and from that point on it does the job.

Since the configuration is a json file, with its freedom in terms of schema, working with it can be pain in the rear. I created a [json schema](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/main/configuration.schema.json) to support editing these files. The [schema needs to be referred in the configuration file](https://stackoverflow.com/questions/75041548/how-to-mark-in-a-json-file-which-schema-describes-it) and the IDE ([at least Jetbrains stuff](https://www.jetbrains.com/dotnet/guide/tips/json-schema/)) can pick the schema up and provide intellisense and validation. For real examples please [take a look at the tests](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/tree/main/Generator.Tests.Unit).

```json
{
    "$schema": "https://raw.githubusercontent.com/EncyclopediaGalactica/RestApiSdkGenerator/main/configuration.schema.json",
    "openapi_specification_path": "Dto/PreProcessing/FileName/filename_preprocessing_should.yaml",
    "target_directory": "Dto/PreProcessing/FileName",
    "solution_base_namespace": "EncyclopediaGalactica.RestApiSdkGenerator.Generator.Tests.Unit.DtoInfoCollection",
    "skip_dto_generating": true,
    "skip_dto_tests_generating": true,
    "skip_request_model_generating": true,
    "skip_request_model_tests_generating": true,
    "test_mode": true
}
```

What I need to decide here is how to validate the configuration. When the generator parses the configuration file it will be deserialized to a POCO and I run [FluentValidation](https://docs.fluentvalidation.net/en/latest/) [against it](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/713213e9c48cf64dbd4ee56ac3b3f05f0bf9f291/Generator/Generator/CodeGenerator.cs#L404). The thing I need to be consider here is that [Json.Net can use the schema for validation](https://www.newtonsoft.com/json/help/html/JsonSchema.htm), however, I don't know how friendly the error message is. I'm really satisfied with FluentValidation and it is very easy to integrate. Performance is not a concern (we are talking about a code generator being run once in a while or most frequently in a CI pipeline), but rather error handling and informing the user and providing well-detailed and easy-to-action messages are the real concern.

## Collecting Dto related data

The generator can collect all the necessary information for generating a valid c# file using [Handlebars](https://handlebarsjs.com/) templates. I implemented a few defensive things like checking whether the letter is a [capital letter after the dot in the namespace](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/713213e9c48cf64dbd4ee56ac3b3f05f0bf9f291/Generator/Generator/CodeGenerator.cs#L140). If not the code modifies the namespace string. The same happens with the property names. They [should start with a capital letter](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/713213e9c48cf64dbd4ee56ac3b3f05f0bf9f291/Generator/Generator/CodeGenerator.cs#L276) however in the openapi yaml file they start with a small capital letter.

Not all the possible problems are covered because there is a dilemma here. All the rules like this can be exported into a yaml validator. Or the generator can throw an exception and stop the execution. Or the generator spits out warnings to the command line. There is a decent amount of things to be considered here. As I move forward I'll figure out what is good for me here.

There are things I don't check yet. [Reserved words...](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/)

## Generatingn Dto classes

Not a lot to say here.

## Testing

I have two test levels. The first one concentrates on [how the code generator transforms the information from the yaml file to a data structure for compiling the template](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/tree/main/Generator.Tests.Unit/Dto/PreProcessing). The [second](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/tree/main/Generator.Tests.Unit/Dto/Generation) one is about the real code generation. There is a lot of things to be done here and a lot of testing opportunity. When I worked for IBM and worked on the generator I missed this part of the testing. We tested the generator changes always running the whole generating process. It is java, it uses maven and the whole is just fucking slow!

The consequence of the above is that [properties](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/713213e9c48cf64dbd4ee56ac3b3f05f0bf9f291/Generator/Generator/CodeGenerator.cs#L30) storing the data for template compiling are public (only `get;` and not `set;`). From code logic point of view they could be, rather must be, private, but it reduces testability.

The other decision I made is to make [the phases possible to disable](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/713213e9c48cf64dbd4ee56ac3b3f05f0bf9f291/Generator/Generator/CodeGenerator.cs#L41). As a result the execution is limited and fast. Let me show you an example. When I test how well the code generator processes the Dto related information I don't need the generation phase to be run. So it is disabled.

I made the decision that every single piece of the code generation process will be tested. I don't like when we have a few tests but they are covering hundreds of small things and you have to dig and figure out why they are failing. The consequence of this decision is that I'll have a lot of tests and higly probable duplicates.

The same granularity is difficult when I test the result. In this case the test code reads the reference and the result file and compares them line by line.

## Code base and "architecture"

This is something I've been considering... I mean, if [I just put all the code in an ugly big class it will work just fine](https://github.com/EncyclopediaGalactica/RestApiSdkGenerator/blob/main/Generator/Generator/CodeGenerator.cs). If I start chopping the codebase into smaller pieces I might create a problem for myself because I won't gain any extra, but I waste time on making the code look better.

The third aspect is that I do not plan to generate any other than c# code.

But...

![](https://media.giphy.com/media/QYLQRR7IF48njkq5an/giphy.gif align="center")

Organizing the code in a meaningful way is a great learning opportunity. For example:

* pulling up the execution control (making it abstract)
    
* separating language properties (for example dealing with reserved words in C#) from how the processing of these happening (generalization and using composite pattern or so...)
    
* generating OOP lang, like C# and java, and script lang like js
    

The whole might increase testability, but I don't see this clearly yet.

![](https://media.giphy.com/media/3o7qDEq2bMbcbPRQ2c/giphy.gif align="center")