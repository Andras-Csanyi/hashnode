---
title: "NeoVim as Java IDE, first steps"
seoTitle: "NeoVim: Getting Started with Java IDE"
seoDescription: "Setting up NeoVim as a Java IDE using LunarVim, fixing bugs, and achieving a functional development environment"
datePublished: Sat Jun 08 2024 12:57:00 GMT+0000 (Coordinated Universal Time)
cuid: clx64drcs00030alcfc9ef37t
slug: neovim-as-java-ide-first-steps
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717852502550/f8e10a5f-4c63-4495-b0f5-1ef1d3eb434c.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1717851730699/9f63b672-0cd0-428d-855d-50b0a8674933.png
tags: java, ides, neovim, lunarvim

---

I have been using LunarVim for a while in order to get more and more familiar with NeoVim world and being able to create the environment where I am comfortable to do good work. I really like Jetbrain's products, but for some reason the hacking/tinkering feeling coming from using NeoVim trumps everything.

I work with java and the first step for me is having a development environment with java similar to what I have with IntelliJ.

There are [starters](https://github.com/LunarVim/starter.lvim) for LunarVim and based on that I started building my own environment. Unfortunately the java-ide starter is buggy and I had to fix it. This [PR](https://github.com/LunarVim/starter.lvim/pull/84) contains the new config. The result is that Eclipse JDTLS works fine, tests can be executed and debugging also works. Enjoy it!