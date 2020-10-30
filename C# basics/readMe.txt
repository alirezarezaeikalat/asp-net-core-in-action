1. There is a difference between function hiding and poly morphism in C# (just for attention)

2. Differences between struct and classes in C#: 

      a. struct is a value type and class is a refference type
                                                                        [attention]
      b. structs are stored in the stack and objects are stored in heap (int is a struct)
          (remember object refference will be store in stack)

      c. value type will be deleted as soon as the scope of the program is completed, but refference types will be destroyed after the 
          garbage collection.

      d. When you copy of a struct into another strcut, a new copy of struct will be created and the modification to one struct will
        not affect the another one, but in classes because of the refference nature, the new instance will not be created and the 
        the modification of one refference will change another one, because the two refference are reffering the same place

      e. structs can't have destructor but classes can have destructor.

      f. structs cannot have less explicit parameter constructor where as a class can.

      g. structs cannot inherit from another struct, but it can inherit from interface.

3. Delegates in c#: 

    delegates are reference type in C#.
    delegates are type safe function pointers (type safe means you can only refer to functions that match the signature of delegate)

      creating delegate type is just like defining a function: 

          public delegate void HelloFunctionDelegate(string Message);

      then you can create instance of defined delegate to use it: 

          HelloFunctionDelegate del = new HelloFunctionDelegate(function that you want)

4. Multicast delegate: 

        multicast delegates points to more than function and when you invoke it, it
        invokes all the functions: 
        in order to use multicast delegate you have define a delegate and 
        add different functions to it: 

            public delegate void HelloFunctionDelegate(string Message);
            HelloFunctionDelegate del = new HelloFunctionDelegate(function that you want)

            // add other function to this: 

                del += function that you want
[attention]
5. in multicast delegates, the functions will be called in order that they are added,
    and if the delegate return a value, the varibale will be the value of the last function
    return type.

6. Method parameters in C#:

        a. if you use ref keyword in function definition and function call you change the call by value to call by refference:

        b. if you use out keyword in function definition and function call you can use it for output: 

                public static void (int FN, int SN, out int Sum, out int Product) {

                }
7. Exception handling in c#: 

    exceptions are just a class, and it has several property that you can use, you can 
    check its properties by going to its definition, there are many exceptions calsses that derive from this class

        try {
            StreamReader reader = new StreamReader(file address);
            Console.WriteLine(reader.ReadToEnd());
        }
        catch(FileNotFoundException ex) {
            Console.WriteLine(ex.Message);
            Console.WriteLine();
            Console.WriteLine();
            Console.WriteLine(ex.StackTrace);
        }
        catch(Exception ex) {

        }
        finaly {    // it will executed nontheless
            // if the reader is null it will throw another exception null refference exception
            if(reader != null) {
                reader.Close()
            }
        }

[attention]
if exception happens, the closest catch block will be executed (from most specific exception to most general exception)

[attention]
8. always pay attention to the exceptions happens in the catch block :)
    in this situation you can pass the original exception with the new exception that is made, and we call this inner exception, 
    and you can get this exception in another try catch block: 

    try{
        try {

        }
        // this exception happened in the catch block
        catch(Exception ex) {
            throw new FileNotFoundException("this is the wrapper exception", ex)
        }
    }
    catch(Exception ex) {
        Console.WriteLine("{0} this is wrapper exception", ex.Message)

        if(ex.InnerException != null) {
            Console.WriteLine("{0} this is the inner exception", ex.InnerException.Message)
        }
    }

[attention]
9. using exception handling for program logic is a anti pattern. you have to implement the program logic outside of the exception 
    handling, and using exception handling only for unforseen errors that might ocuur