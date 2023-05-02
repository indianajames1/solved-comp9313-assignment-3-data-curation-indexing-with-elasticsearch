Download Link: https://assignmentchef.com/product/solved-comp9313-assignment-3-data-curation-indexing-with-elasticsearch
<br>
<h2>1          Problem Statement</h2>




You are given a corpus consisting of a set of legal case reports represented as XML documents and you are asked to create an index that can support fast search across such case reports. Searches can be done based on the text contained in the document as well as based on specific entity types (e.g., person or organization).




In the first scenario above (searching for text contained in the document) your solution must allow for  searching for documents containing general terms such as “criminal law” and “arrest”. In the second scenario, your solution must allow for searching for documents containing specific entities (of a given type) such as organizations (e.g., “Acme Bank”) and person (e.g., “John Smith”).




In order to support queries of the second type (based on entity types), you will need to enrich the original reports with annotations that include mentions (found in the legal case reports) to persons, locations and organizations. The later will require you to use an Entity Recognition API that we will explain later in this document.







<h2>2          Requirements</h2>




The indexing and search engine to be used for the solution to the problem above must be ElasticSearch (version 6.1.*). Your program must be developed as a Spark standalone Application (using Scala), to be built using sbt as building tool. Your Spark standalone program must receive one parameter in input (in its args), which consists in the full path of the directory containing the list of legal case report files (XML files).




You are asked to store all documents in a single index (named legal_idx), with a single type (named cases). It is up to you to decide of the mapping (schema) to be used for this index. Make sure your solution is able to answer the queries explained above (queries based on entity types as well as queries based on general terms). Below we show two examples:




<em>Example: Queries based on entity type </em>







$ curl -X GET “http://localhost:9200/legal_idx/cases/_search?pretty&amp;q=location:Melbourne”

$ curl -X GET “http://localhost:9200/legal_idx/cases/_search?pretty&amp;q=person:John”




<em>  Example: Query based on general terms </em>







$ curl -X GET “http://localhost:9200/legal_idx/cases/_search?pretty&amp;q=(criminal AND law)”







<h2>2          What your program must do</h2>

<strong> </strong>

Your standalone Spark application must:

<ul>

 <li>Create the index (in ElasticSearch) with the corresponding mapping (as specified above)</li>

 <li>Perform the necessary data curation tasks to transform the input data into the representation used for indexing (i.e., XML to JSON representation mapping), and enrich the original data with the three entity types specified above (i.e., person, location and organization)</li>

 <li>Index the curated/enriched data using ElasticSearch</li>

</ul>

<strong> </strong>

<strong> </strong>

<strong> </strong>

<h2>3          Input</h2>

<strong> </strong>

The input for this assignment consists in a list of legal case reports represented as XML files. Each XML file follows the schema below:




<strong>&lt;?xml </strong>version=”1.0″<strong>?&gt; </strong>

<h3>&lt;case&gt;</h3>

<strong>&lt;name&gt;</strong>Sharman Networks Ltd v…<strong>&lt;/name&gt;</strong>

<strong>&lt;AustLII&gt;</strong>http://www.austlii.edu.au/au/cases…<strong>&lt;/AustLII&gt;</strong>

<h3>  &lt;catchphrases&gt;</h3>

<strong>&lt;catchphrase&gt;</strong>application for leave to appeal…<strong>&lt;/catchphrase&gt; </strong>

<strong>&lt;catchphrase&gt;</strong>authorisation of multiple infringements…<strong>&lt;/catchphrase&gt;</strong>

…

<h3>  &lt;sentences&gt;</h3>

<strong>&lt;sentence </strong>id=”s0″<strong>&gt;</strong>Background to the current application…<strong>&lt;/sentence&gt;</strong> <strong>    &lt;sentence</strong> id=”s1″<strong>&gt;</strong>1 The applicants Sharman Networks…<strong>&lt;/sentence&gt; </strong>

…

<h3>  &lt;sentences&gt; &lt;/case&gt;</h3>




The schema above shows that a case is made of:

<ul>

 <li>A name (&lt;name&gt;): This is the title of the case</li>

 <li>Source URL (&lt;AustLII&gt;): The original source of the legal report</li>

 <li>A list of catchphrases (&lt;catchphrases&gt;): These are short sentences that summarize the case</li>

 <li>Sentences (&lt;sentences&gt;): The list of sentences contained in the legal case report</li>

</ul>




You can find a list of legal report cases in the link below (we use a small excerpt from UCI Machine Learning Repository<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a>):

<strong> </strong>

<a href="https://webcms3.cse.unsw.edu.au/COMP9313/19T2/resources/29468">https://webcms3.cse.unsw.edu.au/COMP9313/19T2/resources/29468</a>




<h2>4          Output</h2>




The final output consists in an ElasticSearch index of legal report cases enriched with the entity type annotations as discussed previously.




The resulting index must be able to respond to queries (using ElasticSearch’s search APIs) involving the entity types discussed previously (i.e., person, location, organization) as well as other queries based on general terms (e.g., search for documents that contains the term “criminal law”, search for words appearing in specific properties of the index, etc.).







<h2>5          Named Entity Recognition API</h2>




An important part of enriching the legal reports above is the ability to recognize whether a given term is a mention of one of the entity types of interest (i.e., person, location, organization). For example, in the sentence “<em>The person went to the Apple store located in…</em>”, the entity recognition task must be able to identify that the word “Apple” in this sentence refers to an organization (not a fruit).




For this assignment, you must use the Stanford Core NLP server to help you recognize named entities. You can download the server and get the full documentation of the tool from the link below:




<a href="https://stanfordnlp.github.io/CoreNLP/corenlp-server.html">https://stanfordnlp.github.io/CoreNLP/corenlp</a><a href="https://stanfordnlp.github.io/CoreNLP/corenlp-server.html">–</a><a href="https://stanfordnlp.github.io/CoreNLP/corenlp-server.html">server.html</a>




The Stanford Core NLP server allows you to get access to a number of NLP task through web APIs (HTTP requests). One such task is that of <a href="https://stanfordnlp.github.io/CoreNLP/ner.html">Named Entity Recognition</a> (or NER):




Notice that Stanford Core NLP can be used both as a library inside your Java code or as a server. In this assignment, we will use the Core NLP Server and access the NER functionalities through HTTP requests. This means that you will have to download the sever and run it locally on your computer to get access to the Named Entity Recognition functionality through web APIs (HTTP requests).




Since you will rely on Core NLP for recognizing named-entities, the ability of recognizing person, location and organization will be limited to what is provided by Core NLP (the performance of the entity recognizer will not be part of the assessment of this assignment).







<h2>6          ElasticSearch and Core NLP hostnames / ports</h2>




Make sure your ElasticSearch server is running on http://localhost:9200 and that your Core NLP server is running on http://localhost:9000 (these are their default ports).




Use of these addresses/ports are mandatory, as for assessment, we will assume these servers are running in these hostnames / ports.







<h2>7          Running your Program</h2>




In order to assess your solution, we will build your program using sbt. We will run your program using spark-submit, where we will provide the directory containing the legal case report files (XML files) as argument:




$ spark-submit –class “CaseIndex” –master local[2] 

JAR_FILE FULL_PATH_OF_DIRECTORY_WITH_CASE_FILES




Notice that you must use the name CaseIndex for the object containing your main program.




Make sure your solution can be built and run on VLab (notice that VLab uses Spark 2.4.3 and Scala

2.11.12)




<h2>8          Assignment Submission</h2>

<strong> </strong>

<strong><em>Deadline:</em></strong> 04 August 2019 20:59:59




Log in to any CSE server (e.g. williams or wagner) and use the <em>give command</em> below to submit your solution:




$ give cs9313 assignment3 z9999999.zip




where you must replace z9999999 above with your own zID. The zip file above must contain the following:

<ul>

 <li>Your project directory (which we will use to build your program). Do not include the subdirectories project and target, as these typically occupy lots of space. Include only the sbt file and your src subdirectoy.</li>

 <li>A PDF document, named assignment3_solution.pdf (maximum 2 page, 10 points font-size Arial), that explains your index design and solution implementation (use of figures is highly encouraged to explain your solution). This PDF must also include example queries that show your index in action for general term searches and entity based searches (for all three entity types). Provide queries using the curl command as used in Lab #5 (use only search based on query strings (not DSL)).</li>

</ul>




You can also submit your solution using WebCMS, or Give: <a href="https://cgi.cse.unsw.edu.au/~give/Student/give.php">https://cgi.cse.unsw.edu.au/~give/Student/give.php</a>




If you submit your assignment more than once, the last submission will replace the previous one. The late submission penalty (below) will be applied based on the timestamp of your last submission. To prove successful submission, please take a screenshot and keep it for your own record. If you face any problem while submitting your code, please e-mail the Course Admin (Maisie Badami, <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="c3aeeda1a2a7a2aeaa83b0b7b6a7a6adb7edb6adb0b4eda6a7b6eda2b6">[email protected]</a>)




<strong>9          Late submission penalty </strong>

<strong> </strong>

10% reduction of your marks for the 1st day, 30% reduction/day for the following days.

<strong> </strong>

<h2>10         Assessment</h2>




Your source code will be manually inspected and marked based on readability and ease of understanding. We will run your code to verify that it produces correct results. The code documentation (i.e. comments in your source code) and solution explanation (PDF document) are also important. Below, we provide an indicative assessment scheme (maximum mark: 25 points):




<table width="350">

 <tbody>

  <tr>

   <td width="225">Solution implementation and resulting index</td>

   <td width="125">15 points</td>

  </tr>

  <tr>

   <td width="225">Documentation (PDF document)</td>

   <td width="125">5 points</td>

  </tr>

  <tr>

   <td width="225">Code structure and source code documentation (comments)</td>

   <td width="125">5 points</td>

  </tr>

 </tbody>

</table>

<strong> </strong>

<strong> </strong>


