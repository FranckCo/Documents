# Creation and use of VTL parsers with Antlr

This paper describes how to use the [Antlr](https://www.antlr.org) parser generator to create parsers for the VTL language, and how to use these parsers.

Antlr is a powerful tool that can generate parsers for languages described by formal grammars. These parsers can automatically build parse trees, which are data structures explaining how the language represents a given expression.

Le language used here is [VTL 2.0](https://sdmx.org/?page_id=5096), the Validation and Transformation Language published by the SDMX initiative. VTL has a grammar which is expressed in EBNF notation (Extended Backus-Naur Form).

## Creation of the VTL parsers

In the following code examples, we use a Windows system and suppose that the JAVA_HOME environment variable is set.

Create a working directory and download the latest version of Antlr: we refer here to [version 4.7.2](https://www.antlr.org/download/antlr-4.7.2-complete.jar). Place also in the directory the VTL grammar, described in the `Vtl.g4` and `VtlTokens.g4` files of the [VTL 2.0 package](https://sdmx.org/wp-content/uploads/VTL-2.0-package-2018.07.12.zip).

### Java parser generation

The Java parser and associated artefacts are created with the following command (the `-visitor` option is explained below):

```
java -jar antlr-4.7.2-complete.jar -visitor Vtl.g4
```

We can notice that the VTL grammar produces a warning message:

```
warning(154): Vtl.g4:321:0: rule joinExpr contains an optional block with at least one alternative that can match an empty string
```

The previous command generates several Java classes and related resources: the VTL lexer (or tokenizer), the parser, the listener and the visitor (see [here](https://github.com/antlr/antlr4/blob/master/doc/listeners.md)) and [here](https://saumitra.me/blog/antlr4-visitor-vs-listener-pattern/) for details on the listeners and visitors).

### Parser verification

The TestRig tool can be used to check the parser. We first need to compile the Java classes previously generated:

```
%JAVA_HOME%\bin\javac -cp antlr-4.7.2-complete.jar Vtl*.java
```

We can then analyse a VTL expression, for example (line 2643 of the VTL [reference manual](https://sdmx.org/wp-content/uploads/VTL-2.0-Reference-Manual-20180712-final.pdf)):

```
DS_r := DS_1[calc Me_2 := upper(Me_1)]
```

Save this VTL statement in a `vtl-example.txt` file and run the following command:

```
java -cp .;antlr-4.7.2-complete.jar org.antlr.v4.gui.TestRig Vtl start -tree -ps vtl-example.ps vtl-example.txt
```

This outputs the structure of the statement:

```
(start (statement (varID DS_r) := (expr (exprAtom (ref (varID DS_1))) [ (datasetClause (calcClause calc (calcClauseItem (componentID Me_2) := (calcExpr (expr (exprAtom upper ( (expr exprAtom (ref (varID Me_1)))) ))))))) ])) <EOF>)
```

A `vtl-example.ps` is also produced, which contains an image of the parse tree corresponding to the VTL statement:

![Parse tree](/img/vtl-example.png "Parse tree")

### JavaScript parser generation

The JavaScript artefacts are generated with a specific option added to the previous command:

```
java -jar antlr-4.7.2-complete.jar -Dlanguage=JavaScript -visitor Vtl.g4
```

This creates the JavaScript lexer, parser, listener and visitor in the working directory.

## Using the VTL parsers

### In Java

For an example of how to use the VTL parser in Java, we will write a web service taking a VTL expression and returning the corresponding parse tree as text.

We will use the [quick start example](https://jersey.github.io/documentation/latest/getting-started.html) provided by GlassFish Jersey. If needed, install [Maven](https://maven.apache.org/), then run the following command:

```
mvn archetype:generate -DarchetypeArtifactId=jersey-quickstart-grizzly2 -DarchetypeGroupId=org.glassfish.jersey.archetypes -DinteractiveMode=false -DgroupId=fr.insee.vtl -DartifactId=vtl-service -Dpackage=fr.insee.vtl -DarchetypeVersion=2.27
```

Import the project in your favorite IDE, and rename the resource class from `MyResource` to something more meaningful, for example `Tree`. Rename accordingly the test class (`MyResourceTest` to `TreeTest`). Similarly, we can modify the path element where the service will be exposed: change `@Path("myresource")` to `@Path("tree")` in the resource class and `target.path("myresource")` to `target.path("tree")` in the test class.

To verify that the name changes did not break the service, run `mvn clean test` in the command prompt.

Let us now adapt the resource class to our needs. First, we want the service to take a string parameter corresponding to the VTL expression, so let us modify the `getIt()` method as follows:

```
    @GET
    @Consumes(MediaType.TEXT_PLAIN)
    @Produces(MediaType.TEXT_PLAIN)
    public String getIt(@QueryParam("expression") String expression) {
        return expression;
    }
```

The test class should be changed also:

```
    @Test
    public void testGetIt() {

    	String expression = "DS_r := DS_1[calc Me_2 := upper(Me_1)]";
        String responseMsg = target.path("tree").queryParam("expression", expression).request().get(String.class);
        assertEquals(expression, responseMsg);
    }
```

### In JavaScript

## References

  * The [Antler 4 Documentation](https://github.com/antlr/antlr4/blob/master/doc/index.md)
  * The [Antlr Mega Tutorial](https://tomassetti.me/antlr-mega-tutorial/) by Federico Tomassetti
  * [Compiler in JavaScript using ANTLR](https://medium.com/dailyjs/compiler-in-javascript-using-antlr-9ec53fd2780f) by Alena Khineika
  