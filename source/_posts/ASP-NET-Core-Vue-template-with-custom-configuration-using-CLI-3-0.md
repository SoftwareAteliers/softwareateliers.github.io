---
title: ASP.NET Core Vue template with custom configuration using CLI 3.0
date: 2018-08-16 18:30:47
tags:
---

Lately I become a big fan of Vue.js and, comming from a ASP.NET background, I have to complain about poor quality of templates available for .NET Core. I'm sorry that Microsoft discontinued support for official Vue template and the only ones available for a new dotnet projects are React and Angular. There are some good open source templates out of there, but either outdated version of Webpack and plugins or combination of libraries I don't use make a new project creation time consuming. Fortunately Vue team was working hard over the past few months and now a stable release of it's CLI 3.0 has been released!

What does it mean for us, developers? New CLI aims to minimize the amount of configuration we have to go through to set-up a new project. Even if I find myself very important to understand Webpack configuration and all the magic going behind the scenes, I understand for a beginners can be quite complicated and hard to set up.

Vue CLI 3.0 is based on Webpack 4 and offers out-of-the-box support for:

* Hot module replacement
* Code-splitting
* Tree-shaking
* ES2017 transpilation
* Long term caching and so on

You can choose another optional integrations to fit your needs such as:

* TypeScript
* Progressive Web App
* Vue Router & Vuex
* Linting, unit testing, E2E testing
* 3rd party component frameworks (Vuetify, Vue Bootstrap etc.)
* publish your personal/enterprise plugin and so on

*For a full feature list I suggest you to read the [official CLI release statement](https://medium.com/the-vue-point/vue-cli-3-0-is-here-c42bebe28fbb) by Evan You.*

# Prerequisites

[.NET Core SDK](https://www.microsoft.com/net/download)
`dotnet --version`
`2.1.400`
[Node.js](https://nodejs.org/en/download/)
`node --version`
`v8.11.4`
[Vue CLI](https://cli.vuejs.org)
`npm install -g @vue/cli`
`vue --version`
`3.0.1`
Your favourite editor (I prefer [VS Code](https://code.visualstudio.com/))

# Template

I will describe two ways of template creation: one for developers who want to start with no configuration and the other for people who want to setup their own template and choose custom integrations. I will describe the process using CLI as well as GUI, so everyone can choose their preferred method.

#### CLI Configuration

* [**CLI Template creation**](#CLI-Template-creation): choose custom features and setup a new project under 3 minutes [**RECOMMENDED**]
* [**Clone Template**](#Clone-template-using-CLI): clone git repository and run project with a single command

#### GUI Configuration

* [**GUI Template creation**](#GUI-Template-creation): choose custom features and setup a new project under 3 minutes
* [**Clone Template**](#Clone-template-using-GUI): clone git repository and run project with a single command

## CLI Template creation

### Create .NET Project

There's no Vue template, so we will create a project from React template and modify it to play well with Vue. To create a new .NET Core app just run the following command:
`dotnet new react -o aspnet-core-vue-app`

Open folder in your favorite editor, for VS Code owners:
`code aspnet-core-vue-app/.`

### Modify Template

Change Startup.cs:

Change Vue production build output directory on line 28:

```diff
- configuration.RootPath = "ClientApp/build";
+ configuration.RootPath = "ClientApp/dist";
```

Remove HTTPS redirect middleware for development on line 45 (otherwise it's neccessary to configure ASP.NET Core SSL certificate for a Vue app, it's better to disable it).

```
  if (!env.IsDevelopment())
  {
      app.UseHttpsRedirection();
  }
```

Replace React middleware with generic SPA proxy on line 62:
```diff
- spa.UseReactDevelopmentServer(npmScript: "start");
+ spa.UseProxyToSpaDevelopmentServer("http://localhost:8080"); // your Vue app port
```

### Scaffold Vue application

**Remove all the contents** of the folder /ClientApp and create a new Vue project by using Vue CLI:

`vue create client-app`

> Unfortunately Vue CLI does not allow us to set a project name by C# standards using Upper Camel Case (Pascal Case) naming convention, so let's initiate app inside of client-app folder and then move the content to ClientApp.

**Move all the contents from the new folder /client-app to /ClientApp.**

Now application is ready to run.

### Start the application

> Normally we would run `dotnet run` command to run ASP.NET application and Microsoft ASP.NET Core JavaScriptServices middleware would start a new process for client side application, but middleware does not support Vue CLI 3 out-of-the-box right now, there's a [pull request](https://github.com/aspnet/JavaScriptServices/pull/1726) waiting to be merged.

For now, we can open two command lines and start the app firstly by running Vue app in /aspnet-core-vue-app/ClientApp folder with `npm run serve` command and secondly by running a .NET project in /aspnet-core-vue-app folder with `dotnet run`.

## Clone template using CLI

* Clone starter repository `git clone https://github.com/SoftwareAteliers/asp-net-core-vue-starter`
* Restore client side dependencies by running `npm install` in /ClientApp folder
* Run the Vue app in /ClientApp folder with `npm run serve` command
* Run the .NET application using `dotnet run`

## GUI Template creation

* Use Visual Studio 2017 and create a new ASP.NET Core Web Application using React.js template (or run `dotnet new react` if you don't have VS installed)

* To modify template **follow the same procedure as before**.

* **Remove all the contents** of the folder /ClientApp and create a new Vue project by using Vue CLI 3.0 UI running `vue ui`

* Follow the [wizard](https://cli.vuejs.org/guide/creating-a-project.html#using-the-gui) that guides you through the project creation process. **Do not forget to create the app inside of ClientApp folder.**

## Clone template using GUI