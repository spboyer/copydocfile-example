## copydocfile-example

See GitHub [Issue #795](https://github.com/dotnet/sdk/issues/795) for the details and discussion.

One of the [undocumented changes](https://docs.microsoft.com/dotnet/articles/core/migration/?WT.mc_id=dotnet-0000-shboyer) of converting from project.json to csproj, was the `<DocumentationFile>` no longer automatically copied to the output folder during the build or publish process.

There have been multiple solutions, both `pre` and `post` publish scripts. However, understanding how MSBUILD works and finding the simplest way is key. Thanks to [Eric Erhardt](https://github.com/eerhardt)'s [latest comment here](https://github.com/dotnet/sdk/issues/795#issuecomment-289782712) I think that this is the cleanest way.

Add the following snippet to the .csproj to enable the copy of the documentation file to the output folder.

```xml
  <Target Name="PrepublishScript" BeforeTargets="PrepareForPublish">
    <ItemGroup>
      <DocFile Include="bin\$(Configuration)\$(TargetFramework)\*.xml" />
    </ItemGroup>
    <Copy SourceFiles="@(DocFile)" DestinationFolder="$(PublishDir)" SkipUnchangedFiles="false" />
  </Target>

<!-- Added by Visual Studio, Visual Studio for Mac, or hand code in other IDE -->
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' ">
    <DocumentationFile>bin\Debug\netcoreapp1.1\copydocfile-example.xml</DocumentationFile>
  </PropertyGroup>
  <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|AnyCPU' ">
    <DocumentationFile>bin\Release\netcoreapp1.1\copydocfile-example.xml</DocumentationFile>
  </PropertyGroup>
```

It supports the **F5** options as well as the `dotnet build` / `dotnet publish` CLI commands.

Another important option tested was the ability to pass a custom **output** folder using the `--o|--output` paramter.

```console
dotnet publish -c Release -o myreleasefolder
```

## Other options

You can also add additional output locations to the `<DocumentationFile>` node.

```xml
 <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|AnyCPU' ">
    <DocumentationFile>bin\Debug\netcoreapp1.1\copydocfile-example.xml</DocumentationFile>
    <!-- creates the file in the root of the project -->
    <DocumentationFile>copydocfile-example.xml</DocumentationFile>
    <!-- Creates the file in a "docs" folder -->
    <DocumentationFile>docs\copydocfile-example.xml</DocumentationFile>
  </PropertyGroup>
```

---

> [tattoocoder.com](https://tattoocoder.com) &nbsp;&middot;&nbsp;
> GitHub [@spboyer](https://github.com/spboyer) &nbsp;&middot;&nbsp;
> Twitter [@spboyer](https://twitter.com/spboyer)
