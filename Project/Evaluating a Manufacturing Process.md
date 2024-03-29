# Manufacturing Process

Manufacturing processes for any product is like putting together a puzzle. Products are pieced together step by step and it's important to keep a close eye on the process.

For this project, you're supporting a team that wants to improve the way they're monitoring and controlling a manufacturing process. The goal is to implement a more methodical approach known as statistical process control (SPC). SPC is an established strategy that uses data to determine whether the process is working well. Processes are only adjusted if measurements fall outside of an acceptable range.

This acceptable range is defined by an upper control limit (UCL) and a lower control limit (LCL), the formulas for which are:

ucl = avg_height + 3 * (stddev_height/√5)

lcl = avg_height - 3 * (stddev_height/√5)

Using SQL window functions, you'll analyze historical manufacturing data to define this acceptable range and identify any points in the process that fall outside of the range and therefore require adjustments. This will ensure a smooth running manufacturing process consistently making high-quality products.

### The data

The data is available in the `manufacturing_parts` table which has the following fields:

- `item_no`: the item number
- `length`: the length of the item made
- `width`: the width of the item made
- `height`: the height of the item made
- `operator`: the operating machine

Analyze the `manufacturing_parts` table and determine whether a manufacturing process is working well or requires adjustment:

- Create an alert that flags whether the height of a product is within the control limits using the formulas provided in the notebook. The final query should return the following fields: `operator`, `row_number`, `height`, `avg_height`, `stddev_height`, `ucl`, `lcl`, `alert`, be ordered by the item number. Use a window that includes rows starting 4 rows before the current row up to the current row, however incomplete window rows should be removed in the final query output. Save this DataFrame as `alerts`.

Note: Please also ensure that you do not change the names of the DataFrames that the three query results will be saved as - creating new cells in the Workspace will rename the DataFrame. Make sure that your final solution uses the name provided: `alerts`.

**Approach for the project**

1. Calculate moving average and moving standard deviation

2. Calculate upper and lower control limits

3. Creating an alert to evaluate the manufacturing process

**SOLUTION**

```
SELECT b.**,
       CASE
            WHEN b.height NOT BETWEEN b.lcl AND b.ucl THEN TRUE
             ELSE FALSE
             END as alert
FROM (
     SELECT a.**,
                    a.avg_height + 3 * *a.stddev_height/SQRT(5) AS ucl,
                    a.avg_height - 3 ** a.stddev_height/SQRT(5) AS lcl

      FROM (
                SELECT
                          operator,
                           ROW_NUMBER() OVER w ,
                            height,
                            AVG(height) OVER w AS avg_height,
                            STDDEV(height) OVER w AS stddev_height
                FROM manufacturing_parts
                WINDOW w AS (
                     PARTITION BY operator
                     ORDER BY item_no
                     ROWS BETWEEN 4 PRECEDING AND CURRENT ROW
                         )
                       ) AS a
         WHERE a.row_number >= 5
) AS b
```
