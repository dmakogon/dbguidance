#Polyglot Persistence

##Overview

There are several database types to choose from (relational, key-value, document, column-family, graph), and potentially dozens of
vendor-specific choices for each of these types. There's no simple rule
for choosing.

But more importantly: There's no rule that states only _one_ database type! Sometimes an application may benefit from the use of multiple
data stores, based on common usage patterns within the app. For example, imagine a shopping site, and then imagine this hypothetical
use of databases:

 - Document database for product catalog
 - Graph database for finding related products, based on items placed in cart
 - Relational database for storing purchase history and customer information
 - Column-family database for storing website telemetry data
 - Key-value database for storing shopping cart contents

Now... there are many ways to implement catalogs, related products, purchase history, customer information, telemetry, and shopping carts.
The above list was merely an example. The point, though, is that it's easy to see how specific database engines may be used for specific
purposes.

##Challenges
Of course, there are some challenges (or at least things to think about):

 - Multiple skillsets
 - Multiple HA/DR stories
 - Multiple product licenses