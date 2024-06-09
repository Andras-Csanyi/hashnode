---
title: "Difference in parsing generated sources"
seoTitle: "NeoVim Java IDE cache like problems"
seoDescription: "Exploring differences in parsing GraphQL entities between IntelliJ and NeoVim, and troubleshooting LSP issues for consistent development"
datePublished: Sun Jun 09 2024 18:25:00 GMT+0000 (Coordinated Universal Time)
cuid: clx7vjetf000009l53x45dyv6
slug: difference-in-parsing-generated-sources
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717957377434/7b911e9e-0473-47fb-b135-e3a271075f33.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1717957395018/e521a621-2f6b-4874-9622-9f2f5c4d6cbd.jpeg
tags: java, neovim, lunarvim

---

This is a light bug/interesting anomaly report.

While I was working on my own pet project I started reworking my GraphQL endpoint to do error management following the [Error as Data](https://www.apollographql.com/docs/technotes/TN0041-errors-as-data-explained/) approach. First I tried the way where my java classes representing the GraphQL entities are written by hand. This approach was just uncomfortable as I had to deal with the FasterXML/Jackson annotations. It just felt dirty.

The second approach I tried is using [Kobylynskyi's GraphQL Java Codegen](https://github.com/kobylynskyi/graphql-java-codegen) but since I did not know the correct configuration to add the necessary I run into the same error and struggle.

Anyway, I did what has to be done in cases like this. [Create a small super focused example to find a solution](https://github.com/Andras-Csanyi/neovim_articles/tree/main/error_as_data_pain). So, I did it. This is the part which is relevant to NeoVim.

When I generated the graphql entities IntelliJ parsed correctly the namespaces of the generated entities. As you can see in the screenshots below there are no errors at the imports.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717956729044/3acd9dbc-b25f-4af5-a977-21cb0d74bce5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1717956752399/f8adb8fa-e514-410a-9a11-3d10eec7de1b.png align="center")

While in NeoVim, using [this](https://github.com/Andras-Csanyi/neovim/tree/8f7327a0fd552cf8521162731d39dbedcbee5fbd) config showed the following errors:

* in the generated classes that the package name is incorrect
    
* in the controller class that the imports generated entities representing the GraphQL model are invalid
    

Did not matter how many times I typed `:LspRestart`, or restarted LunarVim or recompiled the project using `mvn clean compile` the error message persisted.

But, as I moved the project to another location in order to be able to link to this article in a manageable way something has changed. When I started LunarVim to make screenshots about the error messages they disappeared. According to NeoVim's status line JDTLS did many things and it seems it cleaned up cache so it could see the current state.

The lesson here is that I need to figure out how can I fully restart the LSP. The result is that I always have IntelliJ open to be able to quick help with it if NeoVim/LSP gets into a strange state. The goal here is to consistently show the same state in NeoVim what IntelliJ shows.