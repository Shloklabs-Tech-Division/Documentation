# Custom API model
API model is used to store the response that we get from the api call. The model value can be manipulated and used.

## Why to use API model.

- It's good practice to maintain the API model seperately. So it will be easy to store and retrive data.

- Use dart built-in function `jsonDecode` to decode the json values. 

- By using the seperate model class will increase the reusability. 

## How to create a API model.

- 1. Create a model class file and add final variable inside that. 

- 2. Create constructor for that class.

- 3. Create a factory method as fromJson, which takes json string input and returns the converted model.

- 4. The method parameter json string will be decoded using the `jsonDecode`.

- 5. Add a empty method, which will return null or empty values. If the json values is empty, return this empty method.

![Alt text](../API/images/model_class.png)

### To handle the list values from json 

- 1. Create another model class to store the values from list.

- 2. Iterate the list value from the parent model class, and pass the value to child model class.

![Alt text](../API/images/sub_model.png)
