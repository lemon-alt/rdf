## When to Reach the Peak Performance Point? An Analysis of RDF Triplestores
We performed an extensive experiments to show the query processing capabilities of well-known triple stores by using their SPARQL endpoints. In particular, we stress these triple stores with multiple parallel requests from different querying agents. Our experiments revealed the maximum query processing capabilities of these triplestores after that it may leads to denial of service (DoS) attacks. We hope this analysis will help triplestores developers to design workload-aware RDF engines to improve the availability of their public endpoints, by avoiding the DOS attacks.
### Persistent URI, Licence:
All of the data and results presented in our evaluation are available online from https://github.com/lemon-alt/rdf under [Apache License 2.0](https://github.com/lemon-alt/rdf/blob/master/LICENSE) .
 ### Datasets and Queries used:
| *Dataset*   | *RDF Dump*  | *Queries* |
|-------------|-----------|----------|
|[DBpedia-3.5.1](http://wiki.dbpedia.org/)|[Download](http://downloads.dbpedia.org/3.5.1/en/)| [Download](https://hobbitdata.informatik.uni-leipzig.de/benchmarks-data/queries/) queries generated by [FEASIBLE](https://link.springer.com/chapter/10.1007/978-3-319-25007-6_4)
|[WatDiv-100M](https://link.springer.com/chapter/10.1007/978-3-319-11964-9_13)|[Download](http://dsg.uwaterloo.ca/watdiv/watdiv.100M.tar.bz2) | [Download](https://hobbitdata.informatik.uni-leipzig.de/benchmarks-data/queries/)
|[WatDiv-1Billion](https://link.springer.com/chapter/10.1007/978-3-319-11964-9_13)|[Download](https://hobbitdata.informatik.uni-leipzig.de/intelligent-SPARQL-interface/) | [Download](https://hobbitdata.informatik.uni-leipzig.de/intelligent-SPARQL-interface/)
### Triplestores used:
| *Triplestore*| *Download* | *Related info* |
|--------------|------------|----------------|
|[Virtuoso](https://link.springer.com/chapter/10.1007%2F978-3-642-04329-1_21)|[here](https://github.com/openlink/virtuoso-opensource/releases)|Set the ```virtuoso.ini``` file accrodingly. However, the file we used in our experiments is given [here](https://github.com/lemon-alt/rdf/blob/master/virtuoso.ini). |
|[Fuseki-TDB](https://jena.apache.org/download/index.cgi)|[here](https://jena.apache.org/download/index.cgi)|Download and unzipp both apache-jena-fuseki-3.13.1 and apache-jena-3.13.1. Follow [this](https://medium.com/@rrichajalota234/how-to-apache-jena-fuseki-3-x-x-1304dd810f09) tutorial for further guidence. |
|[GraphDB](https://www.ontotext.com/)|[Docker Hub](https://hub.docker.com/r/ontotext/graphdb)|`sudo docker run -p 127.0.0.1:7200:7200 -v /path/to/dataset/files:/path/to/dataset/files --name <contianer_name> -e "GDB_JAVA_OPTS= -Dgraphdb.workbench.importDirectory=/path/to/dataset/files" ontotext/graphdb:9.0.0-free` |
|[Blazegraph](https://blazegraph.com/)|[here](https://github.com/blazegraph/database/releases/tag/BLAZEGRAPH_2_1_6_RC)|After unzip, run the `BlazegraphStah.sh` script, as given [here](https://github.com/lemon-alt/rdf/blob/master/blazegraphStart.sh). |
|[Parliament](http://ceur-ws.org/Vol-517/ssws09-paper2.pdf)|[here](https://github.com/SemWebCentral/parliament/releases)|After unzip run `./StartParliament.sh`, given [here](https://github.com/lemon-alt/rdf/blob/master/StartParliament.sh) to start the server and then in  new terminal run `java -cp "clientJars/*" com.bbn.parliament.jena.joseki.client.RemoteInserter <hostname> <port> <inputfile> [<graph-name>]` to upload dataset.|
### Benchmark execution:
We used [IGUANA](https://link.springer.com/chapter/10.1007/978-3-319-68204-4_5), a benchmark execution framework, which can be downloaded from [here](https://github.com/dice-group/IGUANA). We set the `iguana.config` file (as given [here](https://github.com/lemon-alt/rdf/blob/master/iguana.config), for all the individual experiments according to below given guidlines:

*  `connection1.service=http://localhost:7200/sparql` set the SPARQL endpoint address.
* `connection1.update.service=http://localhost:7200/sparql` (optional) used for update/write operations.             
* `sparqlConfig1=32, org.aksw.iguana.tp.tasks.impl.stresstest.worker.impl.SPARQLWorker, 180000, <path/to/queries.txt file>, 0, 0`
Here 32 shows number of workers, 180000 milli second = 3 minutes is query time out, and stresstestArg.timeLimit=3600000 (in mili seconds, 1 hour, time to complete one experiment). 


Once the config file is ready, then start experiment by following below steps:
* Inside the parent IGUANA folder, start IGUANA by `./start-iguana.sh`. Just after it has started successfully, open a new terminal and start the experiment by sending the `iguana.config` file to the iguana's core processor. This can be done by running the command `./send-config.sh iguana.config`. 
* Expect the results in the same folder as `results_*.nt` file. Extract Queries-per-Second (QpS) out of the file through a command `grep queriesPerSecond <result_file> > new_file`. This `new_file` contains only QpS values in RDF format (object of the triple shows QpS value).
* Upload this `new_file` to Virtuoso (in our case), run SELECT query to get all the QpS values and save it as `CSV` file. Add these values and take the average. This is the QpS value (throughput) of the triplestore for given number of parallel users.
* Repeat the experimets for different number of users, and then for other triplestores in the same manner. *NOTE:* Ensure the availability of endpint before starting IGUANA - Also, a bash file `all_exp_script` shown [here](https://github.com/lemon-alt/rdf/blob/master/all_exp_script) can be edited with little effort to perform all the experiments in one go. 
### Evaluation results:
All the results obtained were in `.nt` format, but we converted them to `CSV`, as discussed above. The `Triplestores_Results.xlsx` file contains all the results along with plots, which can be downloaded from [here](https://github.com/lemon-alt/rdf/blob/master/overall_results.pdf) and individual results of all the benchmark datasets are available at the below links:

[DBpedia3.5.1-FEASIBLE results](https://github.com/lemon-alt/rdf/tree/master/Dbpedia_FEASIBLE_results)

[WatDiv-100-Million triples results](https://github.com/lemon-alt/rdf/tree/master/watDiv_100_Million_results)

[WatDiv-1-Billion triples results](https://github.com/lemon-alt/rdf/tree/master/watDiv_1_Billion_results)

[WatDiv-10-Million triples results](https://github.com/lemon-alt/rdf/tree/master/watDiv_10_Million_results)

### Authors:
* Annonymous 
