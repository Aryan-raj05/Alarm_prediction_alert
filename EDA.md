## --> Cleaning Data


| Column                  | Use in Model? | Missing Action                              | Reasoning                                                                                                               |
| ----------------------- | ------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `Site Name`             | **Yes**       | ✅ Drop row                                  | Site is a primary key for sorting, grouping, interarrival time — without it, you can’t compute alarm timelines.         |
| `Alarm Name`            | **Yes**       | ✅ Drop row                                  | High-cardinality categorical used in classification tasks (e.g., next alarm type). You need valid alarm type text here. |
| `Specific Problem`      | Optional      | ⚠️ Impute `"UNKNOWN"`➕ Add `_missing` flag  | Often overlaps semantically with `alarm_name`. Don’t drop rows unless you're training a text/NLP model on it.           |
| `Previous Severity`     | Optional      | ⚠️ Drop rows if using✅ Else: fill with mode | If it's derived or only used for escalation prediction, drop null rows **or** impute based on alarm type/target.        |
| `Additional Text`       | ❌ No          | ✅ Drop column                               | Very sparse and free-text. No structured use unless you're planning NLP-based root-cause detection later.               |
| `Number Of Occurrences` | ✅ Yes         | ⚠️ Drop if nullOR✅ Fill with `0`            | Most models expect a number. If missing, it likely means it’s the **first occurrence**, so fill with `0` is reasonable. |
|                         |               |                                             |                                                                                                                         |


## Feature Engineering


| Unique Value Count | ML Readiness                              |
| ------------------ | ----------------------------------------- |
| =1                 | Drop (constant column)                    |
| =2                 | Binary – Encode as 0/1                    |
| ≤10                | Categorical – OneHotEncode                |
| 11 to 100          | High Cardinality – Label/Frequency Encode |
| >100               | Very High – NLP, Hashing, or Drop         |
