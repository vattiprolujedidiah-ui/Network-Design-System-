#include <iostream>
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

struct Edge {
    int u, v, weight;
};

// Disjoint Set Union
class DSU {
    vector<int> parent, rank;

public:
    DSU(int n) {
        parent.resize(n);
        rank.resize(n, 0);

        for (int i = 0; i < n; i++)
            parent[i] = i;
    }

    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);

        return parent[x];
    }

    bool unite(int x, int y) {
        x = find(x);
        y = find(y);

        if (x == y)
            return false;

        if (rank[x] < rank[y])
            swap(x, y);

        parent[y] = x;

        if (rank[x] == rank[y])
            rank[x]++;

        return true;
    }
};

// Prim's Algorithm
void prims(vector<vector<int>>& graph, int V) {
    vector<int> key(V, INT_MAX);
    vector<int> parent(V, -1);
    vector<bool> mst(V, false);

    key[0] = 0;

    int total = 0;

    cout << "\n--- Prim's Algorithm ---\n";

    for (int count = 0; count < V; count++) {
        int u = -1;

        for (int i = 0; i < V; i++) {
            if (!mst[i] &&
                (u == -1 || key[i] < key[u]))
                u = i;
        }

        if (u == -1 || key[u] == INT_MAX) {
            cout << "Graph is disconnected.\n";
            return;
        }

        mst[u] = true;
        total += key[u];

        for (int v = 0; v < V; v++) {
            if (graph[u][v] != 0 &&
                !mst[v] &&
                graph[u][v] < key[v]) {

                key[v] = graph[u][v];
                parent[v] = u;
            }
        }
    }

    cout << "Selected Edges:\n";

    for (int i = 1; i < V; i++) {
        cout << parent[i] << " - "
             << i << " : "
             << key[i] << endl;
    }

    cout << "Minimum Cost: " << total << endl;
}

// Kruskal's Algorithm
void kruskals(vector<Edge>& edges, int V) {
    sort(edges.begin(), edges.end(),
         [](Edge a, Edge b) {
             return a.weight < b.weight;
         });

    DSU dsu(V);

    int total = 0;
    int count = 0;

    cout << "\n--- Kruskal's Algorithm ---\n";
    cout << "Selected Edges:\n";

    for (auto e : edges) {
        if (dsu.unite(e.u, e.v)) {
            cout << e.u << " - "
                 << e.v << " : "
                 << e.weight << endl;

            total += e.weight;
            count++;
        }

        if (count == V - 1)
            break;
    }

    if (count != V - 1) {
        cout << "Graph is disconnected.\n";
        return;
    }

    cout << "Minimum Cost: " << total << endl;
}

int main() {
    int V, E;

    cout << "MINIMUM SPANNING TREE PROJECT\n";

    cout << "Enter number of buildings: ";
    cin >> V;

    if (V <= 0) {
        cout << "Invalid number of buildings.\n";
        return 0;
    }

    cout << "Enter number of connections: ";
    cin >> E;

    vector<vector<int>> graph(
        V, vector<int>(V, 0));

    vector<Edge> edges;

    cout << "Enter connections (source destination cost):\n";

    for (int i = 0; i < E; i++) {
        int u, v, w;
        cin >> u >> v >> w;

        if (u < 0 || u >= V ||
            v < 0 || v >= V ||
            u == v || w <= 0) {
            cout << "Invalid edge. Try again.\n";
            i--;
            continue;
        }

        graph[u][v] = w;
        graph[v][u] = w;

        edges.push_back({u, v, w});
    }

    prims(graph, V);

    kruskals(edges, V);

    return 0;
}
