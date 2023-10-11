# DBeaver

## Change active db
```
set search_path to 'new_db_name';
```

## Commit mode
DBeaver supports two modes for committing changes to the database:
* Auto-commit transfers all changes that you make immediately to the database.
* Manual commit requires your confirmation before committing a change to the database or rolling it back.
It can be useful to check query time in manual mode.

## Transaction log
The Transaction Log shows all transactions (queries of ‘transaction’ type such as INSERT/DELETE/UPDATE and others) made during the current DBeaver session.

## Query Execution Plan
If a database driver supports the visualization of the execution plan, you can see the execution plan of the current query (under cursor) by pressing Ctrl+Shift+E or clicking Explain execution plan on the context menu or in the SQL Editor toolbar

## Database Structure Diagrams
The ER diagram for a table shows the table itself and its relations with other tables inside the schema. To view the ER diagram for a table or view, double-click the table or view in the Database Navigator and then, in the Database Object Editor, switch to the ER Diagram tab

## PGDebugger
[PGDebugger](https://dbeaver.com/docs/dbeaver/PGDebugger/)

## Pending Transactions
It might be useful to check your pending transactions because they might lock your database. To see your pending transactions, click the arrow next to the Transaction Log button in the toolbar
The upper table of the Pending transactions window shows currently active connections and the number of their transactions. The bottom table shows query details of the connection that is currently in focus in the upper table:
When the Pending transactions window opens, the upper table shows only those connections that have pending transactions. If no connections have pending transactions, the table is empty. To see all connections that are currently active (connected), select the Show all connections checkbox.

You can commit or roll back transactions right from the Pending transactions window: in the upper table, click the row with the required uncommitted transactions and then click the Commit or Rollback button, depending on your purpose. If a transaction is committed/rolled back successfully, both buttons are disabled. If the operation is unsuccessful, the system displays an error message.

To see all previous transactions made during the current session, select the Show previous transactions checkbox. To see all queries including non-transactional ones, select the Show all queries checkbox. The green rows are committed transactions, the orange (or red) ones are rolled back, rows without a special color are non-transactional or pending transactions.

