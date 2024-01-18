In this article, we'll walk through the process of implementing a multi-select functionality in Dynamics 365 Finance and Operations (D365 FO) using X++. 
 
![Screenshot ](https://github.com/Kibetdonald/Multiselect-Dialog-D365/assets/50916200/053a3246-96c2-4a6f-b4a9-7c0f711b85be)


 ## Step 1: Initialize a SysLookupMultiSelectCtrl
To begin, we need to create a table, a query, and a form to store and display the values for multi-select. The table will contain the necessary data, and the form will be the interface for users to perform multi-select actions. In the form's code, we initialize a SysLookupMultiSelectCtrl to handle the multi-select functionality.

```
[DataSource]
class TargetTable
{
    public void init()
    { 
        super();
        multicltrl = SysLookupMultiSelectCtrl::construct(element, SourceTable, querystr(SourceTableQuery));
    }
}
```

Step 2: Implementing Multi-Select in the Field
In the field where multi-select will be performed, create a modified method and incorporate the following code. This method ensures that selected values are captured and stored in the target field's container.
```
[Control("String")]
class TargetField
{
    public boolean modified()
    {
        boolean ret;
        container c, v;
        container combinedContainer;
        int i;
        TargetTable targetTbl = TargetTable_DS.cursor();
                
        ret = super();
        if (ret)
        {
            c = multicltrl.get();
            v = multicltrl.getSelectedFieldValues();
                            
            for (i = 1; i <= conLen(c); i++)
            {
                info(conPeek(c, i));
                combinedContainer += conPeek(c, i);
            }

            ttsbegin;
            TargetTable_DS.query().dataSourceTable(Tablenum(TargetTable)).addRange(fieldNum(TargetTable, RecId)).
                value(SysQuery::value(targetTbl.Recid));
            targetTbl.selectForUpdate(true);
            targetTbl.TargetFieldCon = combinedContainer;
            targetTbl.update();
            ttscommit;
        }

        return ret;
    }
}
```
## Explanation:
We initialize the SysLookupMultiSelectCtrl in the TargetForm to facilitate multi-select functionality.
In the TargetField method, the modified method captures the selected values and stores them in the target field's container.
Transactions (ttsbegin and ttscommit) ensure data consistency during the update process.

## Conclusion:
By following these steps and utilizing the provided code snippets, you can seamlessly implement multi-select functionality in Dynamics 365 Finance and Operations using X++. This approach enhances user experience and provides a robust solution for handling multiple selections.
