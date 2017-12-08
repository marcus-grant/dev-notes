# Semiotic D3 & React Library Notes

## Overview
- Cover the layers and philosophical underpinnings of this architecture
- `XYFrame` manages spatial relationships of data, hence the two axis are numerical in relationship
- `ORFrame` are ordinal frames that display categorical vs numerical relationships
- `NetworkFrame` display only the nature of connected relationships of data

## Frame Props Notes
- size is an array of 2 numbers representing the width & height of the charting frame
  - this size gets used to also calculate scaling values in the charting frames

## ORFrame
- Ordinal frames
- [ORFrame API Docs][02]
- They show ordinal relationships between data
  - *categorical arrangements of data*
- Types of charts:
  - Pie charts
  - Windrose
  - Bar Charts
  - Stacked Bars
  - Windmill Charts
  - Histograms
  - Swarm charts

### Pie Charts with ORFrame
- `rAccessor` shouldn't be defined as the range will be static
- columns are effectively represented as each pie slice and are dynamically sized based on the associated value
- `projection` needs to be `"radial"` inorder to render data on the ordinal axis in a circle
- `dynamicColumnWidth` is needed for pie charts because columns become pie slices when projected radially and the value given to this prop determines the value that sizes the column to represent smaller or larger pie slices
- `oAccessor` should of course refer to a key that represents members of the ordinal axis
- `type` **needs** to be used to specify a `"bar"` type of graph
  - without this prop, nothing renders, it took a while to figure this out

##  Data goals
- 

## References
[01]: https://emeeks.github.io/semiotic/#/semiotic/ "Semiotic Overview"
[02]: https://github.com/emeeks/semiotic/wiki/ORFrame "ORFrame API Docs"

1. [Semiotic Charting Library Overview][01]
2. [Semiotic: ORFrame API Docs][02]

