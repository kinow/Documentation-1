# RDF All The Things (RATT)
This RATT documentation assumes the following knowledge:
- Basic understanding of typescript
- Basic familiarity with linked data and TriplyDB

RATT is a [Typescript package](https://www.npmjs.com/package/@triply/ratt) developed by Triply B.V., in order to easily be capable of developing an ETL (Extract, Transform, Load)  process and transforming existing data to linked data. 
The core idea of RATT is that input data is processed record for record, thus making the ETL process faster.

## Defining a RATT app
First, you need to define a RATT app. In this step, you can specify the default graph, sources and destinations. The `defaultGraph` will be the graph of your output linked data. Also with `destinations`, you set where your data will be stored and, with `sources`, you set the input data that will be used in the ETL process.
   ```sh
  const app = new Ratt({
     defaultGraph: "https://example.org/",
    cliContext,
    destinations: {
      out: Ratt.Destination.TriplyDb.rdf("test-etl-boilerplate"),
    },
    sources: {
      input: Ratt.Source.file("rdf/input/test.trig"),
  })
   ```
### Specifying prefixes
When you are defining a RATT app, you can also specify the prefixes used in your data, so that they can then be reused in the script and thus make the code more compact.

```sh
const app = new Ratt({
  cliContext,
  prefixes: {
      rdf: Ratt.prefixer("http://www.w3.org/1999/02/22-rdf-syntax-ns#"),
      rdfs: Ratt.prefixer("http://www.w3.org/2000/01/rdf-schema#"),
  },
  ...
})
```


## RATT runner
The RATT package includes a runner that runs your RATT script for you when you call the following commands:

```sh
yarn
yarn build
yarn run ratt ./lib/etl/main.js
```

In the last command "ratt" is defined in the package.json file in the RATT package as follows:

```sh
"ratt": "lib/runner/cli.js"
```

## Middlewares
The RATT package includes middlewares for all the ETL functionalities, extracting, transforming and loading. Furthermore, there is also a middleware that allows you to validate your data using SHACL. Your data goes through your middlewares using the context.

## Context
The context is a container for the **record** and the **store**. Conceptually the starting middleware that extracts the input data fills the record and gives the context to the next middleware by giving "next" as an argument (see code snippet below).

```sh
app.use((ctx, next) => {
  ...
}
```
In a midleware a new quad can be created. The newly created linked data is then  temporarly contained in the store. Until the end of the ETL is reached. Each record has a new empty store.
```sh
ctx.store.addQuad(...)
```

### Record for record
A starting middleware that extracts the input data from the source goes through the input data record for record. This means that if your input data comes from a csv, the starting middleware will go row for row. Each record goes through all the middlewares before a new record is started.



## Returning a RATT app
If you want to be able of running a RATT script you need to return the RATT app at the end of the script. The the code snippet below.  

```sh
  return app
```

## ETL
Since RATT is developed to help you design ETLs, RATT includes middlewares for extracting, transforming and loading your data. Furthermore, RATT also includes middlewares to validate your data.

### Extract

```sh
#  app.use(mw.fromCsv(Ratt.Sources.file("./dummy.csv"), { delimiter: "," }))
```

## Transform

### Load
You can then load the data using a "toRdf" middleware. This serializes the quads to a file and eventually loads this to the out desitination defined in the RATT app.

```sh
  app.use(mw.toRdf(app.destinations.out))
```


## Upgrading RATT
If you want to upgrade RATT, open a new terminal window and navigate to the repository for which you want to upgrade RATT. Execute the following:
```sh
yarn upgrade-interactive
```
Then with the arrow keys navigate to the ratt package. Press space and then enter. You can check if ratt has been upgraded by:
```sh
yarn list | grep ratt
```
Now you can commit and push the upgraded version of RATT by using for example the following commands:
```sh
  git commit -am "Upgrade RATT" && git push
```
