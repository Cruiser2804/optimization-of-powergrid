#include <stdio.h>
#include <stdlib.h>
// define a structure to represent an edge
struct Edge {
 int src, dest, weight;
};
// function to find the set of an element using path compression
int find(int parent[], int i) {
 if (parent[i] == i)
 return i;
 return parent[i] = find(parent, parent[i]);
}
// function to perform union operation of two sets using rank
void Union(int parent[], int rank[], int x, int y) {
 int rootX = find(parent, x);
 int rootY = find(parent, y);
 if (rank[rootX] < rank[rootY])
 parent[rootX] = rootY;
 else if (rank[rootX] > rank[rootY])
 parent[rootY] = rootX;
 else {
 parent[rootY] = rootX;
 rank[rootX]++;
 }
}
// function to optimize a power grid using Kruskal's algorithm
void optimizePowerGrid(char* filename) {
 // read the input graph from the file
 FILE* fp = fopen(filename, "r");
 if (!fp) {
 printf("Error: cannot open file '%s'\n", filename);
 return;
 }
 int V, E;
 fscanf(fp, "%d %d", &V, &E);
 struct Edge edges[E];
 for (int i = 0; i < E; i++)
 fscanf(fp, "%d %d %d", &edges[i].src, &edges[i].dest, &edges[i].weight);
 fclose(fp);
 // sort the edges in ascending order of their weights
 qsort(edges, E, sizeof(struct Edge), compare);
 // allocate memory for parent array and rank array
 int parent = (int) malloc(V * sizeof(int));
 int rank = (int) malloc(V * sizeof(int));
 // initialize parent and rank arrays
 for (int i = 0; i < V; i++) {
 parent[i] = i;
 rank[i] = 0;
 }
 // create an empty set to store the edges of the MST
 struct Edge mst[V-1];
 int i = 0, j = 0;
 // iterate over the edges and add them to the MST if they do not form a cycle
 while (i < V-1 && j < E) {
 struct Edge next_edge = edges[j++];
 int x = find(parent, next_edge.src);
 int y = find(parent, next_edge.dest);
 if (x != y) {
 mst[i++] = next_edge;
 Union(parent, rank, x, y);
 }
 }
 // calculate the total cost of the MST
 int cost = 0;
 for (i = 0; i < V-1; i++)
 cost += mst[i].weight;
 // print the edges of the MST and the total cost
 printf("Edges in the minimum spanning tree:\n");
 for (i = 0; i < V-1; i++)
 printf("(%d, %d) weight = %d\n", mst[i].src, mst[i].dest, mst[i].weight);
 printf("Total cost of the minimum spanning tree: %d\n", cost);
 //
