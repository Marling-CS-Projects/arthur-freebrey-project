# package.json

```json
{
	"name": "phaser3",
	"version": "1.0.0",
	"scripts": {
		"start": "parcel src/index.html -p 8080",
		"build": "parcel build src/index.html --out-dir dist",
		"test": "echo \"Error: no test specified\" && exit 1",
		"lint": "eslint ./src --ext .js,.jsx,.ts,.tsx"
	},
	"author": "",
	"license": "MIT",
	"repository": {},
	"devDependencies": {
		"@types/node": "^18.7.23",
		"@typescript-eslint/eslint-plugin": "^2.29.0",
		"@typescript-eslint/parser": "^2.29.0",
		"eslint": "^6.8.0",
		"minimist": ">=1.2.2",
		"parcel-plugin-clean-easy": "^1.0.2",
		"parcel-plugin-static-files-copy": "^2.4.3",
		"typescript": "^3.8.3"
	},
	"dependencies": {
		"modulename": "^0.0.4",
		"phaser": "^3.55.2",
		"sleep": "^6.3.0",
		"wait-for-stuff": "^1.4.0"
	},
	"parcelCleanPaths": [
		"dist"
	],
	"staticFiles": {
		"staticPath": "public",
		"watcherGlob": "**"
	}
}

```
