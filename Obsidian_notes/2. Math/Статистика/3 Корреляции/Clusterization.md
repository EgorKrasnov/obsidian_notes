Basic method is K-means

Framework
1. Place N centroids on the plot
2. Classify observations by [Euclidean distance](https://en.wikipedia.org/wiki/Euclidean_distance "Euclidean distance")
3. Place each centroid on middle of cluster
4. Repeat from stage 2 while new classification will not be the same as previous


How to define number of centroids. 
Each additional centroid decrease total within-cluster sum of squares
![[Number of centroids.png|300]]
Just choose optimal number of clusters :)



