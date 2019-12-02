# Interactive Dependency Tree

## Create an aggregated dependency graph as text
Run 
```shell
mvn com.github.ferstl:depgraph-maven-plugin:3.3.0:aggregate -DmergeScopes=true -DgraphFormat=text -Dincludes=org.camunda.bpm.* -DincludeParentProjects=true -DshowGroupIds=true -DshowVersions=true -DshowTypes=true -P!distro-ce,!distro-webjar
``` 
on the project, e.g. `camunda-bpm-platform/pom.xml`.

## Adjust the dependency information
1. open `camunda-bpm-platform/target/dependency-graph.txt`
1. replace a couple of things here in order to comply to the Maven Dependency Plugin output 
  (basically move the packaging information before the version information):
   * replace `7.13.0\-\d+\.\d+\-\d+` (regex) by `7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:jar` (no regex) by `jar:7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:war` (no regex) by `war:7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:rar` (no regex) by `rar:7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:ejb` (no regex) by `ejb:7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:zip` (no regex) by `zip:7.13.0-SNAPSHOT`
   * replace `7.13.0-SNAPSHOT:pom` (no regex) by `pom:7.13.0-SNAPSHOT`

## Digest the information into an interactive tree
1. setup a little Java Maven project with
   * a dependency on 
   ```xml
   <dependency>
     <groupId>org.apache.velocity</groupId>
     <artifactId>velocity</artifactId>
     <version>1.7</version>
   </dependency>
   ```
   * the compiled jar from https://github.com/adutra/maven-dependency-tree-parser as library
   * a Main like the following (`<path-to-dependency-graph-txt>` should point to `camunda-bpm-platform/target/dependency-graph.txt`)
   ```java
   public static void main(String... args) {
     try {
       InputType type = InputType.TEXT;
       Reader r = new BufferedReader(new InputStreamReader(new FileInputStream("<path-to-dependency-graph-txt>"), "UTF-8"));
       Parser parser = type.newParser();
       Node tree = parser.parse(r);
	   File outputDir = new File("<path-to-local-dir>");
       // create TreeTable
       VelocityRenderer renderer = new JQueryTreeTableRenderer();
       renderer.setOutputDir(outputDir);
       renderer.setFileName("index_treetable.html");
       renderer.visit(tree);
       // create JSTree
       renderer = new JQueryJSTreeRenderer();
       renderer.setOutputDir(outputDir);
       renderer.setFileName("index_jstree.html");
       renderer.visit(tree);
     } catch (Exception e) {
       throw new RuntimeException("Did not work", e);
     }
   }
   ```
5. execute the Main and accompany the resulting `index_jstree.html` and `index_treetable.html` by the content from https://github.com/adutra/maven-dependency-tree-parser/tree/master/src/main/resources/static
6. the TreeTable and JsTree can be inspected with the HTML files
