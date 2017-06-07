Documentation
# Coding conventions

# Programming

Rules:
No \_hungarian notation
Enums are not pluralized
Classes are not pluralized

| **Code** | **Case** | **Example** |
| --- | --- | --- |
| Global variables | lowerCamelCase | itemsToLoopTrough |
| Local variables / parameters | lowerCamelCase | itemsToLoopTrough |
| Method / function names | UpperCamelCase | CalculateDate |
| Classes / interfaces / Enums | UpperCamelCase | PaperTowel |
| Constant | FULLY UPPERCASE with underscores between words | MAX\_WIDTH |
|   |   |   |
|   |   |   |
|   |   |   |
| **Designer, we use prefixes for the GUI items** |   |   |
| Textbox |   | TbUserName |
| Label |   | LblUserName |
| Button |   | BtnLogin |
| Listview |   | LvCustomers |
|   |   |   |

More additional naming guidelines: [https://msdn.microsoft.com/en-us/library/ms229002.aspx](https://msdn.microsoft.com/en-us/library/ms229002.aspx)

# Database

We use a T-sql database.

Rules:
Object names are easily understood
Id&#39;s are used as primary keys
Table names are not pluralized
Table or column names do not start with tbl or clmn (we know it&#39;s a column)
Table and column names may not match
Abbreviations are allowed on really long words but the definition needs to be clear.

| Table | PascalCase | CustomerInformation |
| --- | --- | --- |
| Columns | PascalCase | UserName |
| Statements | FULLY UPPERCASE | SELECT, FROM, WHERE, DESCENT, GROUP BY ect.   |
| **Prefixes allowed in (with cammelcase):** |   |   |
| View |   | vwNotPayingCustomers |
| Function |   | udfCalculateMonthlyLoan |
| Triggers |   | trigOnCalculate |
