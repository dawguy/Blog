# Config Changes

Got frustrated by the compilation errors due to setting values in ngOnInit. Got rid of them with this property in tsconfig.json.

https://stackoverflow.com/questions/49699067/property-has-no-initializer-and-is-not-definitely-assigned-in-the-construc

```
"strictPropertyInitialization": false,
```
