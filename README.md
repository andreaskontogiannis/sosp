# The Computational Complexity of Avoiding Strict Saddle Points in Non-Convex Constrained Optimization

This notebook accompanies *The Computational Complexity of Avoiding Strict Saddle Points in Constrained Optimization*. It supports the paper's unified group analysis and cell classification using exact symbolic calculations.

The notebook checks the prescribed polynomial exclusion inequalities, enumerates the local color-and-arrow patterns from the piecewise construction, and verifies the listed transformations to group representatives. The paper provides the mathematical justification for the finite reduction and for transferring the representative bounds to construction cells.

## Requirements and running

- Wolfram Mathematica with a notebook interface. The supplied notebook was saved with Wolfram 14.3; compatibility with older versions has not been tested here.
- The verification notebook, `SOSP_Complete_Verification_analysis.nb` (downloaded copies may have a numbered suffix).
- No external packages or data files are required.

1. Open the notebook in Mathematica.
2. Start with a fresh kernel, using **Evaluation → Quit Kernel → Local** if a kernel is already running.
3. Choose the settings in the first input cell.
4. Select **Evaluation → Evaluate Notebook** and allow evaluation to finish.

Evaluate the notebook from top to bottom: later sections use definitions from earlier sections. Explanations appear in text cells and commands in input cells. If a section is collapsed, expand its cell group using the bracket at the right.

## Settings

The supplied defaults are:

```wolfram
seconds = 30;
useResolve = False;
epsG = 1/10^10;
epsH = 1/10^10;
```

The default runs the exact Bernstein coefficient checks. To additionally run real quantifier elimination, change the setting to:

```wolfram
useResolve = True;
```

`seconds` limits each rectangle's `Resolve` call and each rectangle's Bernstein check separately; it is not a limit on the complete notebook. Quantifier elimination can take substantially longer than the Bernstein checks. Increase this limit if a call times out. Preserve the exact rational constants rather than replacing them with decimal approximations.

## Verification steps

### 1. Polynomial exclusion inequalities

`cornerData` and `regions` transcribe the representative corner data and assigned rectangles. The notebook reconstructs the biquintic polynomial, differentiates it, and checks the paper's signed gradient or negative-curvature criterion on each rectangle.

For Groups 1–4, the checks cover every corner Hessian parameter `sigma` in `[-1/2, 1/2]`, as required by the negations in the classification. For Groups A–G, `sigma = -1/2`.

The affine coefficient test reduces each margin to a strictly positive constant term and nonnegative coefficients of the unbounded gap parameters. The required parameter endpoints are checked explicitly. Two methods check these same polynomial conditions:

- **Bernstein coefficients:** after mapping the rectangle to the unit square, the least Bernstein coefficient supplies an exact lower bound on each polynomial.
- **Quantifier elimination (optional):** `Resolve[..., Reals]` checks the universally quantified inequalities on the closed rectangle.

The coverage check verifies that the certified rectangles lie in the unit square, have disjoint interiors, and have total area one. There are **34 assigned rectangles across 11 groups**.

### 2. Exhaustive local patterns

`cellTests` transcribes the piecewise color and arrow conditions, preserving their priority. The enumeration retains the four corner labels before applying any group transformations.

It enumerates coordinate offsets modulo six, compatible local node states, and corridor endpoints within the finite order abstraction explained in the notebook. `smallSize = 6` represents the relevant index orderings; it is not a numerical test of one six-node circuit. The expected enumeration produces **865 configurations and 57 distinct patterns**. Of these, **54 are non-X patterns** and **three are confined to the designated X cells associated with ITER solutions**.

`witnesses` records a first abstract occurrence of each pattern. These records describe local configurations, not necessarily realizations by complete circuits.

### 3. Matching the paper's classification

Each entry of `paperCases` has the form:

```wolfram
{caseLabel, {bottomLeft, bottomRight, topLeft, topRight},
 group, transformation, sign}
```

Corner labels such as `"red-right"` specify a color and a **descent arrow**. Stored gradient vectors point in the opposite direction. A sign of `1` means no negation; `-1` means negation.

The code checks the transformation prescribed in each row, rather than searching for an alternative match. It verifies the transformed arrows and the representative's actual corner-value conditions. Value bounds are checked symbolically for all allowed positions and side lengths `n >= 10^40`. Negation is permitted only for Groups 1–4.

Finally, it checks that the case list contains every enumerated pattern, without duplicates, and that the X patterns do not occur among the non-solution cells.

## Reading the output

A successful complete run ends with:

```text
All polynomial groups verified: True
865 configurations; 57 patterns; 3 patterns confined to X cells.
Paper classification verified: True
Polynomial checks and paper classification verified: True
```

The polynomial summary reports the number of rectangles certified by `Resolve` and by Bernstein bounds, together with a coverage result for each group. With `useResolve = False`, the `Resolve` counts are zero because that method was skipped.

**The final success flag requires at least one method to certify each rectangle; it does not require both methods to succeed.** To establish that both methods independently certify all rectangles, enable `Resolve` and check that both method counts equal the `Rectangles` count in every group.

Useful diagnostic variables are:

| Variable | Contents |
| --- | --- |
| `results` | Group, rectangle, criterion, and the two method results |
| `bernsteinBounds` | Exact lower bounds for the coefficient polynomials |
| `summary` | Per-group method counts and coverage results |
| `patterns`, `witnesses` | Enumerated patterns and their abstract occurrences |
| `report` | Case-by-case classification results |
| `completeVerificationPassed` | Combined final Boolean result |

Only a literal `True` is counted as a successful check. A timeout, `$Aborted`, or an unevaluated expression does not certify an inequality. A failed Bernstein bound alone does not imply that the inequality is false: that sufficient certificate may be inconclusive.

Saved output cells record an earlier evaluation. Re-evaluate the notebook to reproduce the checks on your installation.

## Scope

The notebook supports the specific group inequalities and classification encoded from the paper. It does not automatically extract formulas from the LaTeX source or verify the entire hardness reduction. The finite-enumeration justification, transformation argument, and separate constrained-boundary analysis remain part of the paper's proof.

Moreover, the authors provide the hardness_analysis.ipynb (Jupyter notebook, python3), within the same folder, where one can find python code that generates the polynomial expressions of the biquintic interpolation for Gx, Gy and lambda, for each one of the above groups. In the same file, one can also find code for plotting the landscapes of the function, the partial derivatives and the lambda for each one of the above groups
