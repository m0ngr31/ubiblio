(Screenshots at the end of this document)

# Features:

1. Add books
2. Remove books
3. Update books
4. Search books and see their location (useful if stored in bins, because you don't have space for tons of bookshelves)
5. Autopopulate a book's fields by entering an ISBN (either by typing /isbn/[your-isbn]) into the address bar, or from the 'Add New Book" interface
6. Reading list management for each user
7. Book wishlist (just set owned=False, then update to True when you buy it)
8. Withdraw and return books. Display a list of withdrawn books (to help put them away).
9. Admin users can access all features. Non-admin can only search, manage their reading list, and withdraw/return books.
10. Content discovery (browse by genre).
11. Works on most phones (browsing by genre will only work in landscape though, because of the sometimes long book summaries)
12. Really quite fast, low memory requirements for hosting (I typically see under 100MB and tiny CPU usae).
13. No distractions -- it does what it needs to do and nothing else.

No support for cover images. Wanted to keep memory footprint down and performance lightning fast. Besides, who judges books by the cover?


# Why this exists:

1. It took about a day to build (well, at least until I thought of a few more features...).
2. I wanted something fast to manage a few hundred books. I tried Koha and others but the huge memory footprint (~4GB) made them expensive to self-host. This application runs with less than 100MB of memory, so I can cram it on a server that hosts lots of other stuff.
3. I wanted a way to quickly check if I own books from my phone, while visiting book sales / stores. Also a way to get a list of books I want. Since I read in English and French, but live in country where neither are common languages, this was a super important feature for me. Opportunities to buy a lot of books are few and far-between for me, so I have to carpe the diem pretty optimally.


# To Do:

1. If I have hundreds of books in a genre, content discovery with "browse by genre" is a bit cumbersome. When a genre is selected, I could display authors + a textbox with summary of their works. Then clicking that leads to a deisplay of all books of that genre by that author. This would let me scale to thousands of books more easily. Not high priority. The software is basically done.
2. I might add an endpoint that lets admin users add other users. In case I open my personal library to the public one day or whatever.

# HTML Theme
The HTML theme is modified from "forty" by HTML5UP (https://html5up.net/). Actually, check out their other themes too. They are excellent and provide the themes under CC Attribution 3.0 (https://html5up.net/license). It's a huge timesaver for building things like this. So please leave the attribution in the footer.

# Menu System
![Screenshot of the menu system](https://github.com/seanboyce/ubiblio/blob/main/ubiblio_menu.png)

# Book Search
![Screenshot of the book search system](https://github.com/seanboyce/ubiblio/blob/main/ubiblio_search.png)

# Reading list / Browse by Genre / Wishlist / Withdrawn Books List
![Screenshot of a more detailed list of books](https://github.com/seanboyce/ubiblio/blob/main/ubiblio_readling_list.png)
