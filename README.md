The following document outlines the functionality of the Sales Order User Event scripts.

## Before Load

### When Viewing an Order

Adds the commission button to the order form.  
Adds the Approve button to the order form, which can only be viewed by the Admin or Senior Data Entry role

### When Creating an New Order

Initializes fields on the order form.

Sets two body fields to an empty string[^1].
* Amount Paid (custbody_order_amountpaid) [^5]
* Payments On Time Until  (custbody_order_paymentsontime)

[^1]: Don't know why these fields are set
[^5]: This field seems to be 0 for all orders 

#### When creating an order from an opportunity

Checks if there is a status on the opportunity, and if there is a status on the opportunity[^2], copies a few fields from the opportunity to the order. 

| Field From | Sales Order Field To |
| --- | --- |
| Order Type | Order Type |
| Expected Close Date | Service Start Date  |
| First Order | First Order |

Sets Service end date to the current date + 12 months.

If the opportunity has a status of `Rejoiner` then the  `Order Status` field is set to `Renewal` else is set to `New`.
If the opportunity has a status of `Rejoiner` then the column `Revenue Type` on the item sublist is set to `Rejoiner` else is set to `New Business`.


[^2]: Can an opportunity not have a status?


Checks if there is a partner on the Sales Order and if there is, copies a few fields from the partner to the order[^3]. 

| Field From | Sales Order Field To |
| --- | --- | 
| First Name | Partner First Name | 
| Last Name | Partner Last Name |
| Email | Partner Email |
| Phone | Partner Phone |
| Pay Code | Pay Code |
| Class | Partner Class |

[^3]: Can a Sales Order not have a partner and why is this only done when creating an order from an opportunity?


#### When not creating an order from an opportunity

Sets `Start Date` to the current date and `End Date` to the current date + 12 months.

Here is a table with all the fields that are copied to the order from other records.


### When Editing an Order
> Note this also applies to when creating or copying a new order

Adds a `Create Renewal` checkbox[^4] if a field `custbody_order_renewalopp` is set to true.

[^4]: This field is not stored. Does this trigger another script to run?



For each of the core membership items in the sublist, if the current rate on the transaction is less than 150[^6] then sets the rate to the current price of a core membership item[^7]. 


[^6]: Why is this 150?
[^7]: Should this happen when editing an order or only when creating a new order?



## Before Submit


No code will run when either of the following conditions are true:

* The order is not approved [^9]
* The user is being edited from a list
* The order is being edited from the Web Services API[^8]

[^8]:  Not sure where your editing from the Web Services API
[^9]:  Not sure why certain functionality such as copying the partner class should only happen when the order is approved



If the field Order Status (`custbody_order_status`) is set to Canceled then 
the script will set the hidden field `isclosed` to true on the all items on the item sublist. 
This will cancel the order.

The script will determine if the partner is added or changed on the order.
If the partner is added or changed then the script will copy the partner organization to the order.
The script will also determine if the partner is in their first year and set the "Is First Year Rep" field appropriately.

When there isn't a partner on the order the script will set the Class field to the  Corporate Sales Region.

The script sets the department to Standard.

The amount of Core Memberships on the order is calculated and stored in the field "Is Multi Core"[^10].

[^10]: This field name seems to indicate that its a boolean but it actually stores the number of core memberships on the order. Not an issue but the name is misleading.

If the record is new and the week number is not set, the script performs a search using the current date to find a week that starts before and ends after the current date.[^11]

[^11]: Are you sure you want to use the current date and not the sales effective date?




