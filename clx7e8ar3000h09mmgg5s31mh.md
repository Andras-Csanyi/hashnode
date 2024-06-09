---
title: "NeoVim as Java IDE checklist"
seoTitle: "NeoVim Java IDE capabilities and expectations checklist"
seoDescription: "NeoVim Java IDE: code formatting, file management, test execution, debugging, IntelliSense, build system support"
datePublished: Sun Jun 09 2024 10:20:28 GMT+0000 (Coordinated Universal Time)
cuid: clx7e8ar3000h09mmgg5s31mh
slug: neovim-as-java-ide-checklist
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717928378123/255f339d-38b1-44ac-b769-a00ea052a537.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1717928394168/1d879d5d-1198-4872-85d3-dc4285be6a8b.jpeg
tags: java, ides, neovim

---

This article is a checklist to show what capabilities NeoVim as Java IDE has. I don't have a formulated list in my mind. I use IntelliJ on daily basis and I consider its features as basic and default things. They aren't.

The point of this list is seeing if NeoVim as Java IDE is ready for to be used in corporate environment or not.

On the other hand, if I decide to start a project to provide better tooling for NeoVim java then this article is a good start to identify requirements.

The configurations I am going to try out are based on [LunarVim](https://lunarvim.org).

*This article is continuously updated.*

### <mark>The reference is always IntelliJ.</mark>

# Code formatting

* annotation placement for fields is configurable
    
* annotation placement for classes/types are configurable
    
* import order
    
    * lexical order
        
    * raw order
        
* import grouping
    
    * JDK packages
        
    * Own packages
        
    * static imports
        
* spaces between import group
    

# Code formatting check

# File management

* add a new class/interface/record
    
    * package name added
        
    * class/interface/record/enum name added
        

# Test execution

# Debugging

# IntelliSense

* when type is added its import automatically added
    

# Snippets

# Refactoring

# Build systems support

## Maven

* adding package
    
    * showing the available versions of the given package
        
    * intellisense for the package name
        
    * intellisense for the package id
        
* pom.xml reload
    
    * checks if the configuration is correct
        
* package configuration
    
    * intellisense to the package specifics