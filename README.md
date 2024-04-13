# OpenAPI Wrapper

Wrapper around OpenAPI generator, crafting server and client classes in a jar for Maven deployment that will simplify your workflow, amplify your efficiency.

Seamlessly integrate microservices architecture with ease.

## Usage

1. Put spec file into `specs` folder
2. Update the name of the spec file in parent's `pom.xml` file `inputSpec`
3. Publish to maven repository
```shell
mvn clean package
```
4. The dependencies will look like
    - **client** - groupId: `com.genten.openapi` artifactId: `client`
    - **server** - groupId: `com.genten.openapi` artifactId: `server`
