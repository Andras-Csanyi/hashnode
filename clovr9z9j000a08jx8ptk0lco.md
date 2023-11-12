---
title: "Dev Diary #2"
datePublished: Sun Nov 12 2023 17:34:48 GMT+0000 (Coordinated Universal Time)
cuid: clovr9z9j000a08jx8ptk0lco
slug: dev-diary-2
tags: spring, java, graphql, hibernate, dev-diary

---

A week ago I spent a few hours with Encyclopedia Galactica, especially its repository level which means Hibernate. I'm not familiar with Hibernate at all, I would say I'm way better with EF Core than with Hibernate, moreover EF Core is superior for me. Anyway, I hit walls around one-to-many relations. Hibernate's conception seems to be more complicated than EF Core's, but it is probably due to that I don't know Hibernate well. The whole made me frustrated.

During the week the idea I already shared on X came into my mind. I don't need relations at database level when the endpoint is pure GraphQL. Graphql will attach entities for me, what the repository should only do is read them out from database, map them to objects and push it up to GQL level.

%[https://x.com/csanyi_andras/status/1722941617819009505?s=20] 

If I want to go this direction then the MVC approach I started with HTMX won't work. Either I have to call the service `/graphql` endpoint to have a pure GQL thing and also have in the same time the breeze and comfort HTMX and MVC provides, or I have to deal with repository (Hibernate) level joins. Eventually I have to deal with Hibernate, it is not a question.

If I go with pure GQL without defined schema on database level (this is what is provided by a SQL server) I'll have a problem when I'm going to build a REST api. REST does not do any entity joins for me. The hacky solution come into my mind is to have two set of entities, one for GQL repositories and the other one for REST repositories and they connect to the same database... and the disaster is there... two Hibernate context on the same database... terrible idea. A better idea is to have separate methods for GQL so they just pull up the data and lazy loading is not even close.

And the urge came to go back and use Dotnet technologies. Razor MVC is similarly good as Spring MVC, HTMX can work with both very well. Working with EF Core and solving this "when joined entities are needed" problem is solved explicitly due to their `.thenInclude()` method where one can define if the certain joined entities should be read or not. But, I resisted the surge of dropping the java shit because I work in a java environment and I have future plans and I need the synergy.

Anyway, I'll experience with one entity - one table approach for GQL and we will see how it works. I also will figure out what to do when beside GQL there is a REST endpoint too.