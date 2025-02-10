# TS Generation

## Option 1: Use CI/CD stage

```yml
name: Deploy NPM Package

on:
  push:
    branches:
      - main

jobs:
  publish:
    runs-on: ubuntu-latest

    steps:
      - name: Install pnpm
        run: npm install -g pnpm

      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v4
        with:
          node-version: 21
          cache: pnpm

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Generate OpenAPI specs for ts-client
        run: pnpm generate

      - name: Publish NPM package
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
        run: npm publish
```

## Option 2: Use openapi-generator-maven-plugin

```xml

<plugin>
  <artifactId>openapi-generator-maven-plugin</artifactId>
  <executions>
    <execution>
      <configuration>
        <apiPackage>${swagger.root}.client.api</apiPackage>
        <configOptions>
          <basePackage>${swagger.root}.client</basePackage>
          <modelPackage>${swagger.root}.client.model</modelPackage>
        </configOptions>
        <generatorName>typescript-fetch</generatorName>
      </configuration>
      <goals>
        <goal>generate</goal>
      </goals>
    </execution>
  </executions>
  <groupId>org.openapitools</groupId>
</plugin>
```

## Option 3: Fine-tune specifications using CLI dependency

```xml

<plugin>
  <artifactId>exec-maven-plugin</artifactId>
  <executions>
    <execution>
      <configuration>
        <arguments>
          <argument>-jar</argument>
          <argument>${project.build.directory}/openapi-generator-cli.jar</argument>
          <argument>generate</argument>
          <argument>-i</argument>
          <argument>${project.parent.basedir}/specs/api.yml</argument>
          <argument>-g</argument>
          <argument>typescript-fetch</argument>
          <argument>-o</argument>
          <argument>${project.build.directory}/generated-sources/openapi</argument>
          <argument>--additional-properties</argument>
          <argument>supportsES6=true</argument>
        </arguments>
        <executable>java</executable>
      </configuration>
      <goals>
        <goal>exec</goal>
      </goals>
      <phase>compile</phase>
    </execution>
  </executions>
  <groupId>org.codehaus.mojo</groupId>
</plugin>
```

## Option 4: Use frontend-maven-plugin

```xml

<plugin>
  <artifactId>frontend-maven-plugin</artifactId>
  <executions>
    <execution>
      <configuration>
        <nodeVersion>v21.2.0</nodeVersion>
        <pnpmVersion>8.15.3</pnpmVersion>
      </configuration>
      <goals>
        <goal>install-node-and-pnpm</goal>
      </goals>
      <id>install node and pnpm</id>
      <phase>generate-resources</phase>
    </execution>
    <execution>
      <configuration>
        <arguments>run generate</arguments>
      </configuration>
      <goals>
        <goal>pnpm</goal>
      </goals>
      <id>pnpm generate</id>
    </execution>
  </executions>
  <groupId>com.github.eirslett</groupId>
</plugin>
```
