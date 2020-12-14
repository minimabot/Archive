## Summary
- Try to avoid supporting ancient browsers if possible. Get information of which browsers your customers currently are using and which features you need to support first. It’s important to first look at your analytics data to see which browsers your customers are actually using.
- Consider also using [eslint-plugin-compat](https://github.com/amilajack/eslint-plugin-compat) which provides lint checks for the browser compatibility of your code instead of Babel. This helps you avoid the entire transpiling process altogether and instead allow your code editor to alert you if you’re using any features that are too modern for your customers’ browsers.
- If you really need transpiling, consider also other alternatives like (swc)[https://github.com/swc-project/swc] which is up to 20 times faster than Babel.

## Gloassary
- Transpiling is the process of converting newer language syntax that old browsers can’t understand into the old syntax they recognize.
- Polyfilling is the process of adding the missing methods, properties, or APIs to the old browsers by supplying your own version of the missing native code.

## Link
https://blog.logrocket.com/why-you-dont-need-babel/
