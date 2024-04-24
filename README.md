# Vis Network | Data | Dynamic filtering

when it comes to document filtering capabilities, vis.js itself does not include specific tools or functions for filtering documents, as its primary focus is on visualization rather than data manipulation or filtering.

For document filtering or data manipulation, we would typically process and filter our data using JavaScript or another programming language before handing it off to vis.js for visualization. For example, we can use plain JavaScript, libraries like Lodash or Underscore, or even integrate with data processing frameworks to filter and prepare our data.

If the goal is to integrate filtering capabilities directly within a vis.js visualization (like a network graph or timeline), we would need to implement the filtering logic ourselves. This could involve:

- Creating input elements (like checkboxes, dropdowns, or sliders) that allow users to specify their filtering criteria.
- Handling user input to apply these criteria to the data set.
- Updating the visualization by re-rendering it with the filtered data.

### 1. **Attribute-based Filtering**
We can filter nodes, edges, or other data elements based on specific attributes. For example, we could filter nodes in a network graph by category, type, size, or any custom attribute that we define.

### Example:
Filtering nodes by a category attribute as demonstrated in the previous examples.

### 2. **Range Filtering**
This involves filtering data based on a range of values, such as dates or numerical thresholds. This is useful in timelines or when dealing with graphs that include temporal or scalar data.

### Example:
```javascript
const filteredNodes = new DataSet(nodes.get({
    filter: function (item) {
        return item.value >= minValue && item.value <= maxValue;
    }
}));
```

### 3. **Keyword Search Filtering**
Filter data based on keyword searches. This type of filtering is useful when we have large datasets with textual data.

### Example:
```javascript
const filteredNodes = new DataSet(nodes.get({
    filter: function (item) {
        return item.label.toLowerCase().includes(searchKeyword.toLowerCase());
    }
}));
```

### 4. **Combination Filters**
We can combine multiple filters to refine our data even further. For instance, we could use both attribute-based and range filtering together to narrow down our data visualization to very specific criteria.

### Example:
```javascript
const filteredNodes = new DataSet(nodes.get({
    filter: function (item) {
        return item.category === 'category1' && item.value >= minValue && item.value <= maxValue;
    }
}));
```

### 5. **Exclusion Filtering**
This type of filtering excludes certain data points based on specified criteria, essentially the opposite of inclusion filtering.

### Example:
```javascript
const filteredNodes = new DataSet(nodes.get({
    filter: function (item) {
        return item.category !== 'excludeThis';
    }
}));
```

### 6. **Dynamic Real-time Filtering**
For applications that require real-time data processing, we might implement filters that adjust automatically as new data comes in or as certain conditions change within the application.

### Example:
This might involve setting up WebSocket or other real-time data feeds and updating the DataSet on-the-fly as user inputs or data conditions change.

### 7. **Graphical/Visual Filtering**
In some cases, especially with network graphs, we may want to provide a graphical interface (like sliders, checkboxes, or interactive legends) that allow users to visually select the elements they want to see or hide.

### Example:
Using GUI elements to control what categories of nodes are visible on a graph.

### Implementation in React
In a React application, we manage these filtering operations through state and effect hooks, ensuring the UI stays reactive and the visual components update in response to state changes. This makes React particularly powerful for creating dynamic, interactive visualizations with libraries like `vis.js`.

Sample React Code:

```
import React, { useEffect, useRef, useState } from 'react';
import { DataSet, Network } from 'vis-network';

const NetworkGraph = () => {
    const networkRef = useRef(null);
    const [network, setNetwork] = useState(null);
    const [nodes, setNodes] = useState(new DataSet([
        {id: 1, label: 'Node 1', category: 'category1'},
        {id: 2, label: 'Node 2', category: 'category1'},
        {id: 3, label: 'Node 3', category: 'category2'},
        {id: 4, label: 'Node 4', category: 'category2'}
    ]));
    const [edges] = useState(new DataSet([
        {from: 1, to: 2},
        {from: 2, to: 3},
        {from: 3, to: 4}
    ]));

    useEffect(() => {
        const data = {
            nodes: nodes,
            edges: edges
        };
        const options = {};
        const net = new Network(networkRef.current, data, options);
        setNetwork(net);
    }, [nodes, edges]); // Initializes the network only once

    const handleFilterChange = (event) => {
        const value = event.target.value;
        const filteredNodes = new DataSet(
            nodes.get({
                filter: function (item) {
                    return value === 'all' || item.category === value;
                }
            })
        );
        network.setData({ nodes: filteredNodes, edges: edges });
    };

    return (
        <div>
            <div>
                <label htmlFor="filter">Filter by category:</label>
                <select id="filter" onChange={handleFilterChange}>
                    <option value="all">All</option>
                    <option value="category1">Category 1</option>
                    <option value="category2">Category 2</option>
                </select>
            </div>
            <div ref={networkRef} style={{ width: "600px", height: "400px" }} />
        </div>
    );
};

export default NetworkGraph;
```
