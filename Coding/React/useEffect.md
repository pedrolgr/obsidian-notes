useEffect is a [[Hooks]] from [[React]] that let us synchronize a component with a external system

```javascript

useEffect(() => {

	// setup code
	
	return // cleanup code

}, [/* dependencies */])

```

`Setup code`: the code that is executed when useEffect is called
`Cleanup code`: code to disconnect you from the system (not mandatory, and need to be returned)
`Dependencies`: value that if it changes, the useEffect() is called again after the render


*External system can be an API, third party animation, timer manager, etc...*