# Quadtree-partitioning

Here is an example implementation of quadtree partitioning in C++:

```
#include <vector>

struct Point2D {
    double x, y;
    Point2D(double x_, double y_) : x(x_), y(y_) {}
};

struct QuadtreeNode {
    std::vector<Point2D> points;
    double size;
    Point2D center;
    QuadtreeNode* children[4];
    // constructor
    QuadtreeNode(double size_, Point2D center_) : size(size_), center(center_) {
        for (int i = 0; i < 4; i++) {
            children[i] = nullptr;
        }
    }
};

void insertPoint(QuadtreeNode* node, Point2D point) {
    // check if point is inside the node's bounds
    double halfSize = node->size / 2;
    if (point.x >= node->center.x - halfSize && point.x <= node->center.x + halfSize &&
        point.y >= node->center.y - halfSize && point.y <= node->center.y + halfSize) {
        // if node is at maximum capacity, subdivide it
        if (node->points.size() >= 4) {
            double childSize = node->size / 2;
            double x = node->center.x;
            double y = node->center.y;
            node->children[0] = new QuadtreeNode(childSize, Point2D(x - halfSize, y - halfSize));
            node->children[1] = new QuadtreeNode(childSize, Point2D(x + halfSize, y - halfSize));
            node->children[2] = new QuadtreeNode(childSize, Point2D(x - halfSize, y + halfSize));
            node->children[3] = new QuadtreeNode(childSize, Point2D(x + halfSize, y + halfSize));
            // redistribute points to children
            for (Point2D p : node->points) {
                for (int i = 0; i < 4; i++) {
                    insertPoint(node->children[i], p);
                }
            }
            node->points.clear();
        }
        // add point to node
        node->points.push_back(point);
    }
}

QuadtreeNode* buildQuadtree(std::vector<Point2D>& points, double size, Point2D center) {
    QuadtreeNode* root = new QuadtreeNode(size, center);
    for (Point2D point : points) {
        insertPoint(root, point);
    }
    return root;
}

```

This implementation assumes that a Point2D structure has been defined to represent 2D points with x and y coordinates, and that a vector of Point2D objects has been provided to represent the input data points. The implementation uses a recursive algorithm to subdivide the 2D space into quadrants and store the input data points in the appropriate quadrant. The resulting quadtree is a tree data structure where each internal node represents a quadrant of the 2D space and contains a vector of points that are contained within that quadrant, and each leaf node represents a single point.
