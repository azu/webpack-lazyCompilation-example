## webpack lazyCompilation plugin example

    npm install
    npm run dev

- [Release v5.17.0 · webpack/webpack](https://github.com/webpack/webpack/releases/tag/v5.17.0)
- [add experimental lazy compilation by sokra · Pull Request #12469 · webpack/webpack](https://github.com/webpack/webpack/pull/12469)
- [webpack/examples/lazy-compilation at master · webpack/webpack](https://github.com/webpack/webpack/tree/master/examples/lazy-compilation)

# example.js

```javascript
const libraries = {
	react: () => import("react"),
	acorn: () => import("acorn"),
	"core-js": () => import("core-js"),
	lodash: () => import("lodash"),
	xxhashjs: () => import("xxhashjs"),
	"all of them": () => import("./all")
};

window.onload = () => {
	document.body.style = "font-size: 16pt;";
	const pre = document.createElement("pre");
	pre.style = "height: 200px; overflow-y: auto";
	pre.innerText =
		"Click on a button to load the library with import(). The first click triggers a lazy compilation of the module.";
	for (const key of Object.keys(libraries)) {
		const button = document.createElement("button");
		const loadFn = libraries[key];
		button.innerText = key;
		button.onclick = async () => {
			pre.innerText = "Loading " + key + "...";
			const result = await loadFn();
			pre.innerText = `${key} = {\n  ${Object.keys(result).join(",\n  ")}\n}`;
		};
		document.body.appendChild(button);
	}
	const button = document.createElement("button");
	button.innerText = "Load more...";
	button.onclick = async () => {
		pre.innerText = "Loading more...";
		await import("./more");
		pre.innerText = "More libraries available.";
	};
	document.body.appendChild(button);
	document.body.appendChild(pre);
};
```

# webpack.config.js

```javascript
const { HotModuleReplacementPlugin } = require("../../");

module.exports = {
	mode: "development",
	entry: {
		main: "./example.js"
	},
	cache: {
		type: "filesystem",
		idleTimeout: 5000
	},
	experiments: {
		lazyCompilation: true
	},
	devServer: {
		hot: true,
		publicPath: "/dist/"
	},
	plugins: [new HotModuleReplacementPlugin()]
};
```
