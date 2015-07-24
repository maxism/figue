# Introduction #

This project provides a collection of clustering algorithms implemented in Javascript and open sourced under the MIT licence. The current version supports k-means, fuzzy c-means and agglomerative clustering and provides functions to visualize clustering output (e.g. dendograms).

figue has been successfully tested on IE, Firefox, Chrome and Safari.

Here is an [interactive demo](http://jydelort.appspot.com/resources/figue/demo.html) of figue.

# Using figue #

In order to use figue in your web page, simply link to it in the head section of your HTML page, e.g.:

```
<head>

<script type="text/javascript" src="figue.js"></script>

</head>

```


# K-means #

K-means is one of the most famous clustering algorithm. It is useful to cluster data when the number of clusters is known _apriori_.

The following code shows how to cluster data into 4 clusters with k-means using figue:

```

  // Data source: LinkedIn
  // Initial data are businesses that need to be clustered according to their size (nb of employees) and revenue (in mln$)

  var data = [ 
      {'company': 'Microsoft' , 'size': 91259, 'revenue': 60420},
      {'company': 'IBM' , 'size': 400000, 'revenue': 98787},
      {'company': 'Skype' , 'size': 700, 'revenue': 716},
      {'company': 'SAP' , 'size': 48000, 'revenue': 11567},
      {'company': 'Yahoo!' , 'size': 14000 , 'revenue': 6426 },
      {'company': 'eBay' , 'size': 15000, 'revenue': 8700},
   ] ;

  // Create the labels and the vectors describing the data

  var labels = new Array ;
  var vectors = new Array ;
  for (var i = 0 ; i < data.length ; i++) {
      labels[i] = data[i]['company'] ;
      vectors[i] = [ data[i]['size'] , data[i]['revenue']] ;
  }


  var clusters = figue.kmeans(4, vectors) ;

```

The **kmeans** function takes as input the number K of clusters and a list of N input vectors and it outputs an object with two attributes:
  * **centroids**: an Array of K vectors containing the centroid of each cluster
  * **assignments**: An Array of size N representing for each input vector the index of the cluster

The **kmeans** function will return null if:
  * N < K
  * The number of _different_ input vectors is smaller than K



### Full example ###

The following example cluster data into 4 classes and display the assignments.

```
<html>
<head>

<script type="text/javascript" src="figue.js"></script>

<script type="text/javascript">

function load() {

  // Data source: LinkedIn
  // Initial data are businesses that need to be clustered according to their size (nb of employees) and revenue (in mln$)

  var data = [ 
      {'company': 'Microsoft' , 'size': 91259, 'revenue': 60420},
      {'company': 'IBM' , 'size': 400000, 'revenue': 98787},
      {'company': 'Skype' , 'size': 700, 'revenue': 716},
      {'company': 'SAP' , 'size': 48000, 'revenue': 11567},
      {'company': 'Yahoo!' , 'size': 14000 , 'revenue': 6426 },
      {'company': 'eBay' , 'size': 15000, 'revenue': 8700},
   ] ;

  // Create the labels and the vectors describing the data

  var labels = new Array ;
  var vectors = new Array ;
  for (var i = 0 ; i < data.length ; i++) {
      labels[i] = data[i]['company'] ;
      vectors[i] = [ data[i]['size'] , data[i]['revenue']] ;
  }

  var clusters = figue.kmeans(4, vectors) ;

  var txt ;
  if (clusters) {
    txt = "<table border='1'>" ;
    txt += "<tr><th>Label</th><th>Vector</th><th>Cluster id</th><th>Cluster centroid</th</tr>";

    for (var i = 0 ; i < vectors.length ; i++) {
      var index = clusters.assignments[i] ;
      txt += "<tr><td>" + labels[i] + "</td><td>" + vectors[i] + "</td><td>" + index + "</td><td>" + clusters.centroids[index] + "</td></tr>";
    }
    txt += "</table>"
  } else 
    txt = "No result (too many clusters/too few different instances (try changing K)" ;
  
  document.getElementById('mypre').innerHTML = txt; 

}

</script>

<body onload="load();">

<pre id="mypre"></pre>

</body>
</html>

```


The previous code will display a page showing the following table (note: values may be different depending on the choice of initial centroids):

| Label	|Vector	|Cluster id	|Cluster centroid|
|:------|:------|:----------|:---------------|
|Microsoft	|91259,60420	|1	         |91259,60420     |
|IBM	   |400000,98787	|2	         |400000,98787    |
|Skype	 |700,716	|3	         |9900,5280.666666666666|
|SAP	   |48000,11567	|0	         |48000,11567     |
|Yahoo!	|14000,6426	|3	         |9900,5280.666666666666|
|eBay	  |15000,8700	|3	         |9900,5280.666666666666|


# Hierarchical Clustering #

Hierarchical clustering is useful to cluster data without knowing _apriori_ the number of clusters or just in order to visualize the distance relationships between data items.

The following code shows how to generate a hierarchical cluster using figue:

```

  // Data source: LinkedIn
  // Initial data are businesses that need to be clustered according to their size (nb of employees) and revenue (in mln$)

  var data = [ 
      {'company': 'Microsoft' , 'size': 91259, 'revenue': 60420},
      {'company': 'IBM' , 'size': 400000, 'revenue': 98787},
      {'company': 'Skype' , 'size': 700, 'revenue': 716},
      {'company': 'SAP' , 'size': 48000, 'revenue': 11567},
      {'company': 'Yahoo!' , 'size': 14000 , 'revenue': 6426 },
      {'company': 'eBay' , 'size': 15000, 'revenue': 8700},
   ] ;

  // Create the labels and the vectors describing the data

  var labels = new Array ;
  var vectors = new Array ;
  for (var i = 0 ; i < data.length ; i++) {
      labels[i] = data[i]['company'] ;
      vectors[i] = [ data[i]['size'] , data[i]['revenue']] ;
  }


  var root = figue.agglomerate(labels, vectors , figue.EUCLIDIAN_DISTANCE,figue.SINGLE_LINKAGE) ;

```

Figue can generate hierarchical clusters using the following linkage techniques:
  * single-linkage (figue.SINGLE\_LINKAGE)
  * complete-linkage (figue.COMPLETE\_LINKAGE)
  * average linkage (figue.AVERAGE\_LINKAGE)

Three different types of distances are also supported:
  * Euclidian (figue.EUCLIDIAN\_DISTANCE)
  * Max (figue.MAX\_DISTANCE)
  * Manhattan (figue.MANHATTAN\_DISTANCE)

For more information about hierarchical clustering please refer to [Wikipedia](http://en.wikipedia.org/wiki/Cluster_analysis#Hierarchical_clustering).


### Dendograms ###

Figue can generate graphical representations of hierarchical clusters by means of dendograms such as this one:

```
                                               |               
                                  ___________________________  
                                  |                         |  
                    _____________________________           |  
                    |                           |           |  
        _________________________               |           |  
        |                       |               |           |  
 ______________            ___________          |           |  
 |            |            |         |          |           |  
anna       stephany       tom       marc       john       karin
```


Currently the dendogram can be customized to:
  * control the minimum spacing between nodes
  * be balanced (i.e. all leaves at the same level)
  * display data labels
  * display cluster centroids
  * display distance between centroids

The **buildDendogram** function to produce dendograms is a method that is must be called by the root node, e.g.

```
root.buildDendogram (5, true, true, true, false) ;
```

This example will produce a dendogram:
  * with minimum spacing between nodes: 5 (i.e. 5 blank characters)
  * balanced
  * showing cluster labels
  * showing cluster centroids
  * not showing distance between clusters


### Full example ###

The following example hierarchically cluster data and display the corresponding dendogram.

```
<html>
<head>

<script type="text/javascript" src="figue.js"></script>

<script type="text/javascript">

function load() {

  // Data source: LinkedIn
  // Initial data are businesses that need to be clustered according to their size (nb of employees) and revenue (in mln$)

  var data = [ 
      {'company': 'Microsoft' , 'size': 91259, 'revenue': 60420},
      {'company': 'IBM' , 'size': 400000, 'revenue': 98787},
      {'company': 'Skype' , 'size': 700, 'revenue': 716},
      {'company': 'SAP' , 'size': 48000, 'revenue': 11567},
      {'company': 'Yahoo!' , 'size': 14000 , 'revenue': 6426 },
      {'company': 'eBay' , 'size': 15000, 'revenue': 8700},
   ] ;

  // Create the labels and the vectors describing the data

  var labels = new Array ;
  var vectors = new Array ;
  for (var i = 0 ; i < data.length ; i++) {
      labels[i] = data[i]['company'] ;
      vectors[i] = [ data[i]['size'] , data[i]['revenue']] ;
  }


  var root = figue.agglomerate(labels, vectors , figue.EUCLIDIAN_DISTANCE,figue.SINGLE_LINKAGE) ;

  var dendogram = root.buildDendogram (5, true, true, true, false) ;

  // Render the dendogram in the page (note: pre is handled differently by IE and the rest of the browsers)
  var pre = document.getElementById('mypre') ;
  if( document.all ) { pre.innerText = dendogram ; } else { pre.innerHTML = dendogram ; }
}

</script>

<body onload="load();">

<pre id="mypre"></pre>

</body>
</html>

```

The previous code will display a page showing this dendogram:
```
                                                           |                                    
                                                   94826.5,31102.67                             
                             _____________________________________________________________      
                             |                                                           |      
                      33791.8,17565.8                                                    |      
     ________________________________________________                                    |      
     |                                              |                                    |      
     |                                        19425,6852.25                              |      
     |                           _______________________________________                 |      
     |                           |                                     |                 |      
     |                     9900,5280.67                                |                 |      
     |               _________________________                         |                 |      
     |               |                       |                         |                 |      
     |               |                  14500,7563                     |                 |      
     |               |              __________________                 |                 |      
     |               |              |                |                 |                 |      
91259,60420       700,716       14000,6426       15000,8700       48000,11567       400000,98787
 Microsoft         Skype          Yahoo             eBay              SAP               IBM     

```

# Fuzzy C-means #

The function for calculating fuzzy clusters and instances assignments has recently been added and will be documented soon.

## TODO ##
  * In demo.html add function to randomly generate DS of any size
  * In wiki page, include results of a scalability analysis
  * rewrite demo.html using jquery