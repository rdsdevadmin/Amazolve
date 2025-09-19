## PBS Constraints

1. One and only one assignment of the site per part:
```math
\sum_{i} x_{ri} = 1 \enspace \forall r
```
```json
  {
    "constraint": {
      "CID": "C1",
      "comment": "Constraint #1 - One and only one assignment per part",
      "penaltyVar": "hardPenalty",
      "sumIter": "iterDim",
      "iterDim": "R",
      "iterVars": [
        "r"
      ],
      "exprMain": "EQ(sum_assigned, 1)",
      "sums": [
        {
          "sumIter": "iterDim",
          "iterDim": "S",
          "iterVars": [
            "i"
          ],
          "exprMain": "Xr(r, i)",
          "resultVar": "sum_assigned"
        }
      ]
    }
  }
```

2. Origin and destination for each transport must be different:
```math
\sum_{\left(r,s\right)\in\phi}\sum_{i} x_{ri}x_{si} = 0
```
```json
  {
    "constraint": {
      "CID": "C2",
      "comment": "Constraint #2 - Origin and destination must be different",
      "penaltyVar": "hardPenalty",
      "sumIter": "iterArray",
      "arrayName": "phi",
      "iterVars": [
        "r",
        "s"
      ],
      "sums": [
        {
          "sumIter": "iterDim",
          "iterDim": "S",
          "iterVars": [
            "i"
          ],
          "exprMain": "Xr(r, i) * Xr(s, i)"
        }
      ]
    }
  }
  ```
3. The origin of two sub-parts of a common part must be different:
```math
\sum_{\left(r,s\right)\in\psi}\sum_{i} x_{ri}x_{si} = 0
```
```json
  {
    "constraint": {
      "CID": "C3",
      "comment": "Constraint #3 - Origins of two subparts must be different",
      "penaltyVar": "hardPenalty",
      "sumIter": "iterArray",
      "arrayName": "psi",
      "iterVars": [
        "r",
        "s"
      ],
      "sums": [
        {
          "sumIter": "iterDim",
          "iterDim": "S",
          "iterVars": [
            "i"
          ],
          "exprMain": "Xr(r, i) * Xr(s, i)"
        }
      ]
    }
  }
```

4. Cost in transport between chosen sites must be minimized:
```math
C = \sum_{\left(r,s\right)\in\psi}cost(r_a,s_a)
```
```json
  {
    "constraint": {
      "CID": "C",
      "sumIter": "iterArray",
      "arrayName": "phi",
      "iterVars": [
        "r",
        "s"
      ],
      "sums": [
        {
          "sumIter": "iterDim",
          "iterDim": "S",
          "iterVars": [
            "i"
          ],
          "sums": [
            {
              "sumIter": "iterDim",
              "iterDim": "S",
              "iterVars": [
                "j"
              ],
              "exprMain": "cost(r, i, j) * Xr(r, i) * Xr(s, j)"
            }
          ]
        }
      ]
    }
  }
  ```
## PBS Constants

| Symbol   | Description |
|----------|-------------|
|$hardPenalty$|Penalty applied to unmet constraints|

```json
  {
    "constants": [
      {
        "name": "hardPenalty",
        "def": 10000
      }
    ]
  }
```

## PBS Arrays

| Symbol   | Description |
|----------|-------------|
|$cost$|A database of costs of moving a given part between two locations|
|$phi|The product breakdown structure's parent/child part relationships|
|$psi|The product breakdown structure's sibling part relationships|

```json
[
  {
    "array": {
      "name": "cost",
      "file": "array_cost.json"
    }
  },
  {
    "array": {
      "name": "phi",
      "file": "array_children.json"
    }
  },
  {
    "array": {
      "name": "psi",
      "file": "array_siblings.json"
    }
  }
]
```

## PBS Atoms Configuration

A standard random initialization of the atoms is used and random mutations (or permutations) are applied during the search process.
In addition, the atoms are intialized externally, depending upon the size of the part list and number
of locations.

```json
[
  {
    "params": {
      "initializeType": "Random",
      "mutatorType": "Random"
    }
  },
  {
    "atoms": {
      "file": "atoms.json"
    }
  }
]
```