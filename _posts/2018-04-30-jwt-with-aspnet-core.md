---
layout: post
title: "JWT with ASP.NET Core"
date: 2018-04-30
author: ehsan
categories: [Web Security]
excerpt_separator: <!--more-->
---

JSON Web Tokens are convenient ways to do authentication and authorization on the server.

Some of the benefits of JWTs are:

* It can be consumed by a wider range of clients. In fact by any end-point who can understand HTTP (unline cookies which can be consumed mostly only by browsers).
* You only query the database first time to retrieve them and then you keep using them to authenticate as well as authorize the user.
* You can ask 3rd party identity providers to authenticate your users on your behalf and assign tokens to them.

In microservices architecture, you would probably have a separate Authorization service using [Identity Server](https://github.com/identityserver) or such.

But .NET core can efficiently handle this task as well.

Let’s explore that.
<!--more-->

**1\. Create the Application**

Add an ASP.NET Core 2.0 API application to your solution.

Open the NuGet package manager and add the following packages to your project:

  - [System.IdentityModel.Tokens.Jwt](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/)
  - [Microsoft.AspNetCore.Authentication.JwtBearer](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.JwtBearer/)

**2\. Configure JWT**

First of all, we require a Private (Sign) Key to encrypt/decrypt tokens. We also need to add some specific payload to each token.

One way is to add these configs to appsettings.config:

```javascript
"JwtSettings": {
    "key": "MyLongSymmetricKey",
    "issuer": "http://api.ehsankorhani.com:8080",
    "audience": "BlogReaders",
    "minutesToExpiration": "30"
  }
```

In the application Model folder, create a class to represent these settings:

```csharp
public class JwtSettings
{
    public string Key { get; set; }
    public string Issuer { get; set; }
    public string Audience { get; set; }
    public int MinutesToExpiration { get; set; }
}
```

Then, create a class to read the settings and return an object:

```csharp
public JwtSettings GetJwtSettings()
{
    return new JwtSettings
    {
        Key = Configuration["JwtSettings:key"],
        Audience = Configuration["JwtSettings:audience"],
        Issuer = Configuration["JwtSettings:issuer"],
        MinutesToExpiration = Convert.ToInt32(Configuration["JwtSettings:minutesToExpiration"])
    };
}
```

**3\. Configure authentication service**

In Startup.cs add the following code to ConfigureService method:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var jwtSettings = GetJwtSettings();

    services.AddSingleton<JwtSettings>(jwtSettings);

    services.AddAuthentication(options =>
    {
        options.DefaultAuthenticateScheme = "JwtBearer";
        options.DefaultChallengeScheme = "JwtBearer";
    }).AddJwtBearer("JwtBearer", jwtBearerOptions =>
    {
        jwtBearerOptions.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtSettings.Key)),
            ValidateIssuer = true,
            ValidIssuer = jwtSettings.Issuer,

            ValidateAudience = true,
            ValidAudience = jwtSettings.Audience,

            ValidateLifetime = true,
            ClockSkew = TimeSpan.FromMinutes(jwtSettings.MinutesToExpiration)
        };
    });

    services.AddCors();

    services.AddMvc()
    .AddJsonOptions(options =>
        options.SerializerSettings.ContractResolver =
    new CamelCasePropertyNamesContractResolver());
}
```

This will add Bearer authorization header to the HTTP request/response, add Payloads to token and encrypts it.

**4\. Configure HTTP request**

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseCors(options => options.WithOrigins("http://www.ehsankorhani.com:8000").AllowAnyMethod().AllowAnyHeader());

    app.UseAuthentication();

    app.UseMvc();
}
```

`app.UseCors()` enables us to accept Ajax calls from a different domain.

`app.UseAuthentication()` enables our custom authentication middleware through Authorization filter:

```html
[Authorize]
```

In the next post, I will explain how to create a Token for a user when they log in and authorize them based on their Claims in future requests.
