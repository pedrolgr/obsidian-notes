Controllers are reponsible for handling requests from the client side, send to the next layer and when it receive the request back, send back to the client side

To create a `Controller`, use the decorator `@Controller`.
To create a Get HTTP request method endpoint, use the decorator `@Get`.

If you pass a name inside `@Get()`, its tell to NestJS to create a controller for a specific endpoint using the HTTP method `GET`.

```javascript

@Controller('cats') // not mandatory
export class CatsController{
	@Get('name')
	catsNames(): string {
		return 'cats names here';
	}
	
	otherMethod(): number {
	return 1;
	}
}

```

In this case, `/cats`won't return anything, but `/cats/name/`will.

We can create a controller using the CLI, executing the command `nest g controller [name]`.

We can also use `@Req()`inside our decorator, but doing this way we need to handle the return by ourself like using `res.send(...)`or `res.json(...)`like in [[ExpressJS]], but [[NestJS]] will lose the control.