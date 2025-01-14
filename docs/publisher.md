# Publishing Modules

The `Publisher` component supports publishing modules to various package
managers. It is designed to be attached to a GitHub workflow that takes care of
building the module and uploading a "ready to publish" artifact.

> This component is utilized in `NodeProject` and `JsiiProject` to publish modules.

Supported package managers:

- npm
- Maven
- PyPI
- NuGet
- Go (GitHub)

This is how a publisher is initialized:

```ts
const publisher = new Publisher(project, {
  workflow: releaseWorkflow,
  buildJobId: 'my-build-job',
  artifactName: 'dist',
});
```

`workflow` is a `GithubWorkflow` with at least one job (in this case named `my-build-job`) which is responsile to build the code and upload a GitHub workflows artifact (named `dist` in this case) which will then be consumed by the publishing jobs.

This component is opinionated about the subdirectory structure of the artifact:

|Subdirectory|Type|Contents|
|-|-|-|
|`js/*.tgz`|NPM|npm tarballs
|`dotnet/*.nupkg`|NuGet|Nuget packages
|`python/*.whl`|PyPI|Python wheels
|`go/**/go.mod`|Go|Go modules. Each subdirectory should have its own `go.mod` file
|`java/**`|Maven|Maven artifacts in local repository structure

Then, you should call `publishToXxx` to add publishing jobs to the workflow:

For example:

```ts
publisher.publishToNuGet();
publisher.publishToMaven(/* options */);
// ...
```

See API reference for options for each target.

## Publishing to GitHub Packages

Some targets come with dynamic defaults that support GitHub Packages.
If the respective registry URL is detected to be GitHub, other relevant options will automatically be set to fitting values.
It will also ensure that the workflow token has write permissions for Packages.

**npm**
```ts
publisher.publishToNpm({ 
  registry: 'npm.pkg.github.com'
  // also sets npmTokenSecret
})
```

**Maven**
```ts
publisher.publishToMaven({
  mavenRepositoryUrl: 'https://maven.pkg.github.com/USER/REPO'
  // also sets mavenServerId, mavenUsername, mavenPassword
  // disables mavenGpgPrivateKeySecret, mavenGpgPrivateKeyPassphrase, mavenStagingProfileId
})
```
