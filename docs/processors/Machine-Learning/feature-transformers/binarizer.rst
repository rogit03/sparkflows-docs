Binarizer
=========== 

Binarize a column of continuous features given a threshold.

Type
--------- 

ml-transformer

Class
--------- 

fire.nodes.ml.NodeBinarizer

Fields
--------- 

.. list-table::
      :widths: 10 5 10
      :header-rows: 1

      * - Name
        - Title
        - Description
      * - inputCol
        - Input Column
        - The input column name
      * - outputCol
        - Output Column
        - The output column name
      * - threshold
        - Threshold
        - The features greater than the threshold, will be binarized to 1.0.The features equal to or less than the threshold, will be binarized to 0.0.




