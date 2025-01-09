# LucaP_ERP-02
# LucaP02 The Chart of Accounts
#LucaP

In the ERP application, we have established credits and debits on a ledger with the five major accounts. However, we don't know what those credits and debits do. 

We want the ledger to tell the story of how we moved our money around. Our transaction list from last time 


doesn't do that well. 

Which assets and expenses did we include in the transaction list? That's where the chart of accounts helps. It breaks down Assets, Liabilities, Capital, Expenses, and Revenue into smaller accounts that we can use to describe money in a business more descriptively. 

Let's look at assets as an example. The above transactions have several kinds of assets we can describe as accounts. There's cash and inventory, for example. Lines 2 and 3 above are transactions of buying inventory with cash for $300. We need to get some description into the accounts. 

These accounts form the chart of accounts. Here's an example one


Each account has a Unique Account Identifier. Internally, we'll express an account's use with this number. Notice the first digit of that number. It is common to code a chart of accounts with that numbering system or a similar one to represent the account Category. 



Digits after the first one break the chart of accounts into smaller subsections. For example, all the *110000*'s are *cash*, and the *130000*'s are *inventory*. We can also split those into smaller categories, depending on our situation. I split raw materials into *131100 - Ingredients*, such as flour, and *131200 - Packaging*, such as single-use plates and pizza boxes. You'll want many digits here to accommodate those levels of detail. Hence, I used a six-digit number, though ten or fifteen in functional ERP systems are common. Some systems will use a coded number for different facilities, regions, or othere divisions. 131100-02 may be the Honolulu Store raw materials, and 131100-12 may be the Chicago Store raw materials. 

I want one more thing in the chart of accounts as a convenience: a summary of the current account balances. Instead of running reports to get those balances, many systems will store either a balance or the balance of the credits and debits. 

# Building the schemas
So, if I were to think about this in terms of building an ERP, I'd have the following table:



I'll refer to collections of table plans as a *schema* There are lots of ways to record schemas, often with graphics like this one:  


I'm using a simple spreadsheet method. You'll notice in both schemas that I linked the account category to another file: `AccountCategory`. Instead of adding the account category directly to the account, I placed it in a separate file, making it much easier to expand the possible categories. 

If you've wondered why most databases have so many tables, this is why: for optimal flexibility, anything that is a collection gets a table. You never know when it will change. 

 It does not matter if you are coding to a database or to code. As I'm doing in Swift, you make the schema first. It becomes critical documentation.

## Coding 
For the moment, I'm coding this to a temporary array, building up my features before making it persistent. This is my usual strategy for such projects when not working directly in a database situation. I'd create the table for a database and then be ready to go - it makes debuigging much easier.  

In Swift, the class `AccountCategory` looks like this, 

```
class AccountCategory:Identifiable{
    var id:Int
    var name:String
    
    init(id: Int, name: String) {
        self.id = id
        self.name = name
    }
}
```

We'll make the persistent version from `AccountCategory`. However, I build everything on arrays before adding SwiftData, so I'll make a class for the array. I'll also use this class to populate the array for testing and demonstration. 

```
class AccountCategories{
    var accountCategories:[AccountCategory]
    init(){
        self.accountCategories = [
            AccountCategory(id: 1, name:"Asset"),
            AccountCategory(id: 2, name:"Liability"),
            AccountCategory(id: 3, name:"Investment"),
            AccountCategory(id: 4, name:"Revenue"),
            AccountCategory(id: 5, name:"COGS"),
            AccountCategory(id: 6, name:"Expenses")
        ]
    }  
}
```

With the Account category defined, I'll define Account:

```
class Account:Identifiable{
    var accountNumber:String
    var accountName:String
    var accountCategory: Int
    var isActive:Bool
    var creditTotal:Double
    var debitTotal:Double
    
    //Computed properties
    var id:String{accountNumber}
    var total:Double{debitTotal - creditTotal}
    
    init(accountNumber: String, accountName: String, accountCategory: Int) {
        self.accountNumber = accountNumber
        self.accountName = accountName
        self.accountCategory = accountCategory
        self.isActive = true
        self.creditTotal = 0
        self.debitTotal = 0
    }
    
}
```

Notice the computed properties. `id` satisfies the `Identifiable` protocol, which makes displaying the data easier in lists. `total` can be done in several ways. I could make this a stored `Double` computed in the code for a transaction addition. I did that in this case with credits and debits and will do some subtraction between the two to get my total balance. 

Next, I'll make another array with the chart of accounts. Again, I'll add some test data to work with 

```
class ChartOfAccounts{
    var accounts:[Account]
    
    init(){
        self.accounts = []
        basicAccounts() 
    }
    
    func basicAccounts(){
        //Start with common accounts
        self.accounts = [
            Account(id: "111000", accountName: "Cash on Hand", accountCategory: 1),
            Account(id: "112000", accountName: "Cash in Bank", accountCategory: 1),
            Account(id: "121000", accountName: "Accounts Receivable", accountCategory: 1),
            Account(id: "131000", accountName: "Inventory - Raw Material", accountCategory: 1),
            Account(id: "131100", accountName: "Inventory - Raw Ingredients", accountCategory: 1),
            Account(id: "131200", accountName: "Inventory - Single Use and Packaging", accountCategory: 1),
            Account(id: "132000", accountName: "Inventory - Work in Progress", accountCategory: 1),
            Account(id: "133000", accountName: "Inventory - Assembled Parts", accountCategory: 1),
            Account(id: "134000", accountName: "Inventory - Finished Goods", accountCategory: 1),
            Account(id: "135000", accountName: "Inventory - Purchased Finished Goods", accountCategory: 1),
            Account(id: "161000", accountName: "Production Equipment", accountCategory: 1),
            Account(id: "162000", accountName: "Office Equipment", accountCategory: 1),
            Account(id: "171000", accountName: "Production Equipment", accountCategory: 1),
            Account(id: "172000", accountName: "Office Equipment", accountCategory: 1),
            Account(id: "173000", accountName: "Motor Vehicles", accountCategory: 1),
            Account(id: "173100", accountName: "Motor Vehicles - Food Trucks ", accountCategory: 1),
            Account(id: "211000", accountName: "Accounts Payable", accountCategory: 2),
            Account(id: "311000", accountName: "Common Stock", accountCategory: 3),
            Account(id: "321000", accountName: "Paid in Capital", accountCategory: 3),
            Account(id: "331000", accountName: "Retained Earnings", accountCategory: 3),
            Account(id: "411000", accountName: "Sales Revenues ", accountCategory: 4),
            Account(id: "511000", accountName: "COGS", accountCategory: 5),
            Account(id: "611000", accountName: "Payroll", accountCategory: 6),
            Account(id: "621000", accountName: "Equipment Rental",
                    accountCategory: 6),
            Account(id: "652100", accountName: "Space Rental",accountCategory: 6),
            Account(id: "631000", accountName: "Administrative Expense", accountCategory: 6),
            Account(id: "651000", accountName: "Office Supplies", accountCategory: 6),
            Account(id: "652000", accountName: "Sales and Marketing", accountCategory: 6)
            
        ]
    }
}
```


With this in place, we can add it to the journal we made earlier. 

We're going to do two things. First, I'll add `AccountNumber` as a `String` to `JournalEntry`. 
```
var account:String
``` 

Secondly, I'll make two changes to the initializer. 

The first is to replace description with account:

```
init( account:String,amount:Double,creditDebit:CreditDebit){
```

The second is to initialize account and then place the `accountName` in description. I use `first` to find the row with the appropriate account name in the Chart of Accounts. If you are unfamiliar with `first`, it finds the first instance of an element in an array based on the expression in the curly braces. Since values for `accountNumber` should be unique, it finds the only case that is true. Here, we look for where the `accountNumber` in the chart of accounts `coa` equals the account number in `init`. 

```
self.account = account
let coa = ChartOfAccounts().accounts
if let accountInfo = coa.first(where:{$0.accountNumber == account}){
    self.description = accountInfo.accountName
}else {
    self.description = "Account not found"
}
```

You don't have to add the description as I did here. This code adds some inefficiencies to a lookup of `accountInfo`, but it has a redeeming feature: the name of the account when you made the transaction remains in the journal. If you were to change banks that hold your cash, the bank you used in the past would remain on the transaction. If you fetched the name when you printed the journal, the bank name would show only the current bank for all transactions on this account. 

With this in place, I can change my test entries to

```
var testEntries = [
    JournalEntry(account:"321000", amount: 1000, creditDebit: .credit),
    JournalEntry(account:"111000", amount: 1000, creditDebit: .debit),
    JournalEntry(account:"131100", amount: 300, creditDebit: .debit),
    JournalEntry(account:"111000", amount: 300, creditDebit: .credit),
    JournalEntry(account:"131100", amount: 100, creditDebit: .debit),
    JournalEntry(account:"211000", amount: 100, creditDebit: .credit),
    JournalEntry(account:"621000", amount: 300, creditDebit: .debit),
    JournalEntry(account:"111000", amount: 300, creditDebit: .credit),
    JournalEntry(account:"652100", amount: 200, creditDebit: .debit),
    JournalEntry(account:"111000", amount: 200, creditDebit: .credit),
    JournalEntry(account:"111000", amount: 5000, creditDebit: .debit),
    JournalEntry(account:"411000", amount: 5000, creditDebit: .credit),
    JournalEntry(account:"511000", amount: 400, creditDebit: .debit),
    JournalEntry(account:"131000", amount: 400, creditDebit: .credit)
]
```

Our last step is to use all this. In `TestEntryView`, I'll modify the code for the account and description like this:
 
```
HStack{
    Text(entry.account)
    Text(entry.description)
    Spacer()
}
.frame(width:300)
``` 
 


This code gives me a fixed-width column with the account and description. 

Run this, and you get the transactions with their accounts. 

<Insert app running here>

In this second installment, we follow a pattern: I build the models and schemas first, then the code for interaction. Finally, I add persistence if coded in a computer language. OF course,  persistence exists when I build the schemas in a database system like SQL, but the basic pattern holds. On this first iteration of a general ledger, I've been building the schema piece by piece, and we have one more piece to go -- build journal entries from our transactions. In the next lesson, we'll handle that before taking subsequent lessons to build the interaction.  
