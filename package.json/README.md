

## NPM

- NPM is a standard Node Package Manager for Node.js.
- Alternatives for npm are: yarn and pnpm.
- NPM repository can be `public` or `private`.
- NPM package can be `public`, `private`, or `local`.

If a project has a `package.json` file, run the command `npm install` (or `npm i`) to install all dependencies.
To create a `package.json` file, use one of the commands:
```
npm init (CLI will ask you questions about the project)
npm init -y (no questions, npm will use default values)
```

To install a single package, run the command: `npm install <package-name>`.
Since npm version 5, this command adds the <package-name> to the `package.json` file dependencies. Before version 5, you needed to add the flag `--save`.

Other flags for installing dependency:
| Flag              | Shortcut | Description                                               |
|-------------------|----------|-----------------------------------------------------------|
| `--save-prod`     | `-P`     | Installs and adds to `dependencies` (default behavior)    |
| `--save-dev`      | `-D`     | Installs and adds to `devDependencies`                    |
| `--save-optional` | `-O`     | Installs and adds to `optionalDependencies`               |
| `--save-exact`    | `-E`     | Saves exact version instead of using semver range         |
| `--save-bundle`   | `-B`     | Installs and adds to `bundledDependencies`                |
| `--global`        | `-g`     | Installs package globally                                 |
| `--no-save`       | —        | Installs but doesn't add to `package.json`                |
| `--no-optional`   | —        | Prevents optional dependencies from being installed       |
| `--dry-run`       | —        | Reports what it would install without actually installing |
| `--force`         | `-f`     | Forces re-fetching even if local copy exists              |

When packages are installed, they will be saved in the `node_modules` directory, and the `package-lock.json` will be created.
This file contains concrete versions of each dependency that is installed. This is important for production to make sure you are running the same code version that is tested on your local machine.  Make sure to add this file to the version control system (e.g., Git).
To make sure you have exact dependency versions installed on the machine, run the command `npm clean-install` or `npm ci`.
This command will remove the `node_modules` directory (if present) and install exact versions from the `package-lock.json` file. If `package-lock.json` is not present, this command will throw an error.
- `npm ci`  can only install entire projects at a time: individual dependencies cannot be added with this command.
-   It will never write to  `package.json`  or any of the package-locks: installs are essentially frozen.

To update packages, run the command:
```
// update all packages
npm update

// update specific package
npm update <package-name>
```
### Package.json
Required fields in the `package.json` are:
```
{
  "name": "your-package-name",
  "version": "1.0.0"
}
```
Other fields are:

| Field                | Description
|----------------------|----------
|description           |Short project description
|main                  |Path to the main script
|scripts               |Custom scripts that can be executed
|author                |Author's information
|license               |What license is the project using
|dependencies          |Runtime dependencies
|devDependencies       |Required dependencies for development
|peerDependencies      |Specifies packages that your package is compatible with, but expects the host project to provide them (e.g., a plugin expects React to be installed by the main app)
|bundledDependencies   |They are not fetched from the npm registry but bundled into the `.tgz` file when running `npm pack` or `npm publish`.
|optionalDependencies  |Optional dependencies

The command `npm install` will install all dependencies (except the `bundleDependencies`, which are already included in the package).
To install only dependencies from the `dependencies`, use the command `npm install --production`.
To omit some of the dependencies, use the command: `npm install --omit=dev` for development (same for `optional`, and `peer` dependencies).

### Scripts
NPM has predefined commands like `npm install`, `npm update`, etc.
One of the optional fields in the `package.json` file is `scripts,` and it's used for creating custom scripts.
To run a script from the `scripts` property, use the command `npm run <script-name>`.
Example:
```
{ 
  "scripts": { 
    "start-dev": "node lib/server-development", 
    "start": "node lib/server-production"
  }
}
```
To run start-dev, use the command `npm run start-dev`.

### Executable scripts
- Some packages support executions from the CLI.
- Sometimes it's easy to find the execution file.
Example for webpack:
```
node ./node_modules/webpack/bin/webpack.js
```
- The script can be run from local binaries
-- When a package is installed, if it contains executable files, it will be stored in the `node_modules/bin` directory for local installations, and `/usr/local/lib/node_modules/.bin` (Linux/Mac) or `%AppData%/npm` (Windows) for global installations.
That means, the previous command can be executed like this:
```
./node_modules/.bin/webpack
```
or using the npm shortcut `$(npm bin)` which looks for the `.bin` directory:
```
$(npm bin)/webpack
```
The easiest and most common solution is using the `scripts` property from the `package.json` document:
```
{
  ...,
  scripts: {
    "wp": "webpack"
  }
}
```
To execute this script, run the command `npm run wp`.

From NPM version 5.2, `npx` is included by default, and it helps you find executable scripts in local and global `node_modules`.
This command is the same as the commands above:
```
npx webpack
```
If the `webpack` package is not found in local and global modules, it will install the package and execute it, but it will not save it.
This is great when you need to run a command only once, and you don't need to save the package locally.

### Local Package
Useful if a package is recommended to be installed globally, but you don't want to (or you cannot), or when testing the package before deploying to the npm registry.

From the code, point to the package directory:
```
"dependencies": {
  "my-package": "file:.../my-package",
}
```
Changes in the `my-package` will be reflected in the project that is using this package.

To generate a `.tgz` file, go to the package and run the command `npm pack`.
Example of how to point the `.tgz` file:
```
"dependencies": {
  "my-package": "file:.../my-package-1.0.0.tgz",
}
```
Changes from the package now will not be reflected, because we are pointing to the generated `.tgz` file. We need to generate a new `.tgz` file to reflect the new changes.

For developing or debugging the package, consider using the `npm link` since it reflects all changes without regenerating the package.

### NPM Link
- To link a local package, follow the steps:
1. Open the CLI and navigate to the dependency project.
2. run the command `npm link` - this will create a **global** symbolic link (**symlink**).
3. Navigate to the project where this package should be used.
4. Run the command `npm link <package-name>`. This will create a **local** symbolic link.
5. To remove the **local** symbolic link, run the command `npm unlink <package-name>` or `npm uninstall <package-name>` (unlink is alias for uninstall).
6. To remove a **global** symbolic link, go to the package and run the `npm unlink` or `npm uninstall`.

While using a symlink, you can commit changes to git, it will not affect the code.
To debug a linked package, for the running application in the `launch.json` file add the settings:
```
"runtimeArgs": [  
  "--preserve-symlinks"  
]
```

### Scoped Package
- Regular package except the name starts with the `@` sign (e.g., `@angular/core`).
- Groups linked packages under the same name.
- Avoid naming conflicts (your project might use the same name).
- Tells who the author of the package is.
