# copydocfile-example

Addresses [Issue #795](https://github.com/dotnet/sdk/issues/795)

Since the conversion from project.json format to csproj the `<DocumentationFile>` no longer is automatically copied to the output folder during publish.

Add the following snippet to the .csproj to enable the copy of the documentation file to the output folder.

There have been multiple solutions, both `pre` and `post` publish scripts. However understanding how MSBUILD works and finding the simplest way is key. Thanks to [Eric Erhardt](https://github.com/eerhardt)'s [latest comment here](https://github.com/dotnet/sdk/issues/795#issuecomment-289782712) I think that this is the cleanest way.

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

### Other options as a note

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
