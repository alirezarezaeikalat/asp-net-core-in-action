1. The pattern that you externalize the identity and different apps use this 
    identity is common these days, and the identity is called Security Token Service.

2. First you have to make an identity project in your solution (identity project is 
    a app that uses asp.net core identity), in this project you have to add identity
    nuget package:
    in nuget manager, add this package identityServer4

3. Then add it in ConfigureServices:

    public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllersWithViews();
            services.AddIdentityServer();
        }

4. And it in middleware:

     public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }
            app.UseHttpsRedirection();
            app.UseStaticFiles();

            app.UseRouting();

            app.UseIdentityServer();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
        }

5. In general you have to teach identity server about your users, your client and 
    the resources that it's protecting. there is configuration data that identity
    server needs: 

        a. for teaching the identity server about the user you can use test user
            as an example.

                using System;
                using System.Collections.Generic;
                using System.Security.Claims;
                using IdentityModel;
                using IdentityServer4.Test;
                namespace identityServer
                {
                    public class TestUsers
                    {
                        public static List<TestUser> Users = new List<TestUser>
                        {
                            new TestUser {SubjectId = "818727", Username = "alice", Password ="password",
                                Claims =
                                {
                                    new Claim(JwtClaimTypes.Name, "Alice Smith"),
                                    new Claim(JwtClaimTypes.GivenName, "Alice"),
                                    new Claim(JwtClaimTypes.FamilyName, "Smith"),
                                    new Claim(JwtClaimTypes.Email, "AliceSmith@email.com")
                                }
                            }
                        };
                    }
                }
                and then add this test users to DI (teach the identity server about
                their users):
                    services.AddIdentityServer()
                        .AddTestUsers(TestUsers.Users);

        b. the list of your MVC apps or list of apis should be defined for identity 
            server configuration, and identity server does not mandate the place for
            holding this list, it can be in database or in json file or even hard 
            coded in memory. but you have to appreciate the protocol of identity 
            server for object model, check the Config.cs class as an example of 
            in memory configuration:

            public class Config
            {
                public static IEnumerable<Client> GetClients()
                {
                    return new Client[]
                    {
                    };
                }
            //Identity resources are data like user ID, name, or email address of a user.
            //An identity resource has a unique name, and you can assign arbitrary claim
            //types to it. These claims will then be included in the identity token for
            //the user. The client will use the scope parameter to request access to an
            //identity resource.
                public static IEnumerable<IdentityResource> GetIdentityResources()
                {
                    return new IdentityResource[]
                    {
                        new IdentityResources.OpenId(),
                        new IdentityResources.Email(),
                        new IdentityResources.Profile()
                    };
                }
                public static IEnumerable<ApiResource> GetApiResources()
                {
                    return new ApiResource[]
                    {
                    };
                }
            } 

            and then use it in DI:
                services.AddIdentityServer()
                    // define the digital signature of json web tokens
                    .AddSigningCredential("") or AddDeveloperSigningCredential()
                    .AddTestUsers(TestUsers.Users)
                    .AddInMemoryClients(Config.GetClients())
                    .AddInMemoryIdentityResources(Config.GetIdentityResources())
                    .AddInMemoryApiResources(Config.GetApiResources());

