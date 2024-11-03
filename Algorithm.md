# CirthEnigma Algorithm Overview

The CirthEnigma game involves generating a puzzle based on random matrices and carefully constructed areas. This document details the underlying algorithm in four major stages.

## 1. Generate a Random Matrix

Initialize a matrix called `FIELD` of size `N x N` with random values, such that each row and column contains each number from 1 to N exactly once.\
How to do so?

### Steps:
1. **Fill the Matrix (`FIELD`)**  
   - Initialize a matrix called `FIELD` in the simplest way you can find, such that each row and column contains each number from 1 to N exactly once.

<table>
  <tr>
    <th colspan="5" style="text-align:center;">5x5 matrix example</th>
  </tr>
  <tr>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>4</td>
    <td>5</td>
  </tr>
  <tr>
    <td>2</td>
    <td>3</td>
    <td>4</td>
    <td>5</td>
    <td>1</td>
  </tr>
  <tr>
    <td>3</td>
    <td>4</td>
    <td>5</td>
    <td>1</td>
    <td>2</td>
  </tr>
  <tr>
    <td>4</td>
    <td>5</td>
    <td>1</td>
    <td>2</td>
    <td>3</td>
  </tr>
  <tr>
    <td>5</td>
    <td>1</td>
    <td>2</td>
    <td>3</td>
    <td>4</td>
  </tr>
</table>

2. **Randomly Shuffle Rows and Columns**  
   - To add variability, randomly shuffle the rows and columns of `FIELD`. This ensures that each puzzle generation is unique.
   - It can be shown that by proceeding in this way, the resulting matrix still satisfies the requirement that each row and column contains each number from 1 to N exactly once.

## 2. Create Areas

The next phase involves creating areas within the matrix. Areas represent contiguous areas of the matrix and are used to define groups of cells.

### Steps:
1. **Generate an Auxiliary Matrix (`M`)**  
   - Initialize an auxiliary matrix `M` of size `N x N` filled with zeros (`0`). This matrix will help track areas and their positions.

2. **Locate the First Available Position**  
   - Begin at position `(0, 0)` in matrix `M`. Since it’s initialized with zeros, this position represents the first unassigned area position, referred to as `z`.

3. **Define a New Area (`i`)**  
   - Create a new area `i` as a list (`area-i[]`) to store positions within this area.

4. **Add `z` to the Current Area and Update `M`**  
   - Add the current position `z` to `area-i[]` and mark it as belonging to area `i` by setting `M[z] = i`.

5. **Randomly Select a Direction and Move to the Next Cell**  
   - Choose a random direction and attempt to move one cell from `z` in that direction.
   
6. **Update Position Based on Validity**  
   - Use the function `first_valid_cell(z, direction)`, which tries to move to the chosen position (`z + direction * 1step`).  
     - If this move is invalid (out of bounds or occupied), attempt `z + (direction + 90°) * 1step`, and so on, until all four directions are tried.
     - If all directions are invalid, return `Invalid-Cell`.

7. **Repeat Until Maximum Area Size is Reached or Move is Blocked**  
   - If `z != Invalid-Cell`, go back to step 4. Repeat this process a maximum of `N-1` times.

8. **Locate the Next Available Position in `M`**  
   - Once a area is completed, search `M` for the next available zero position and set it as `z`.  
   - If `z` is valid, increment `i` and repeat from step 3 to define the next area.  
   - If no more zeros are available, all areas have been successfully created.

## 3. Save Areas

After creating the areas, save them as an array, `areas`, which will store the positional data for each area. Each area represents a set of positions still displayed in `FIELD`, allowing specific positions to be removed without compromising puzzle uniqueness.

### Data Structure:
```c
struct area_t {
    u_int positions[];  // Positions within the area
    u_int count;        // Size of positions array
} 

areas = area_t[#areas]  // Save all areas as an array of area_t
```

## 4. Remove Excess Areas

The final phase removes areas to make the puzzle challenging while maintaining a unique solution.

### Steps:
1. **Initialize Counters**  
   - Set `i = 0` to begin with the first area.

2. **Select a Random Position from `areas[i]`**  
   - Randomly select a position `p` within the current area `areas[i]`.

3. **Backtracking Check for Solution Uniqueness**  
   - Test if the matrix remains uniquely solvable if position `p` is removed from `areas[i]` and from `FIELD`:
     - **If no unique solution exists** (multiple solutions arise), revert by:
       - Restoring `p` in `FIELD`.
       - If other positions remain in `areas[i]`, repeat the process starting from "Select a Random Position.". Otherwise, proceed to the next step ("Iterate Through Areas").
     - **If a unique solution remains**, permanently remove `p` from `FIELD` and move to the next step ("Iterate Through Areas").

4. **Iterate Through Areas**  
   - Increment `i`. If `i` reaches `#areas`, reset it to `0` and repeat the process starting from "Select a Random Position."
   - If all positions in every `areas[i]` are processed and removed, the algorithm terminates.

This approach ensures a balanced puzzle by iteratively removing positions in a way that maintains solution uniqueness.

---

## Summary

The CirthEnigma algorithm is structured to:
- **Generate** a randomized base matrix.
- **Define areas** to represent contiguous areas within the matrix.
- **Save areas** for control over puzzle cells.
- **Remove excess areas** carefully to ensure each puzzle has a unique solution while remaining challenging.

This four-step process is the core of CirthEnigma's puzzle generation and guarantees both variety and uniqueness in each generated puzzle.
