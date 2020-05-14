1. You can make new project by ctr + shif + N

2. You can bring back solution explorer by ctrl + alt + L

3. if you add, add new file extension you can use shift + f2 to make new folder

4. you can add file with ctrl + shift + A

5. hold ctrl and press . to show the potential fix

6. We can redirect to certain action in ASP.NET core:

        [HttpPost]
        public IActionResult Edit(Post post)
        {
            return RedirectToAction("Index");;
        }

7. To find about databases, search SQL server object explorer in visual studio,
    and you can connect to local database.

8. This is the example of the DefaultConnection in appsettings.json file for the 
    local database:
        "DefaultConnection": "Server=(localdb)\\MSSQLLocalDB;Database=MyBlog;Trusted_Connection=true;MultipleActiveResultSets=true"