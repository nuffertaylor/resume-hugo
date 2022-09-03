+++
title="Creating a Virtual Bookshelf"
date = "2022-02-14"
+++


A while ago, I saw [this post on reddit.](https://reddit.com) Essentially, the user read a lot of ebooks, but missed seeing the books on the shelf. They took photographs of the book spines, either themselves, or from the internet, then looked up their dimensions, and carefully edited them into their place on the shelf. From what the user said, it was a slow, painstaking process to get everything to look how they wanted. And it's something we can automate.

{{<figure src="https://i.imgur.com/SSqMDRB.jpg" alt="empty_bookshelf" class="right" width="200px">}}

The first thing I needed was a high-quality image of an empty bookshelf from straight-on. Not many images met this exact critera. I found a few pictures online that would work, but a) the resolution wasn't high enough and b) I had no idea what the real-world size of those shelves was.

In order for the book spines to look right on the "virtual" shelf, I needed to know the exact pixel-to-inch ratio, and that ratio needed to be high enough that the spine images placed on the shelf maintained their fidelity and can still be read. Guessing wrong made things look bad. So I emptied a bookshelf in my own house, measured it out, and snapped a picture.


I created a "bookshelf" object. The constructor takes 5 parameters :
- the image file
- the width of a shelf in inches
- the width of a shelf in pixels
- the pixel location of the bottom of each shelf in pixels (not all shelves have consistent heights between shelves, like mine)
- the left edge where the shelf starts (and not the frame)

All of this data needs to be grabbed manually, but once we have it, we can go to work.

Well, sort of. We still need to get images of the actual book spines we want to use. While there are a number of apis to fetch data about books, and images of their covers, currently a book *spine* database doesn't seem to exist, even though [people have been asking for years](https://www.reddit.com/r/books/comments/11xt4u/is_there_an_online_database_of_book_spine_pictures/) It looked like I would have to collect the images of the book spines myself. I track all my reading on goodreads, so I knew exactly what I was looking for.

For books I physically owned, it was pretty easy. Just find the book, snap a picture, and edit it down to just the spine. For ebooks and audiobooks, it was more challenging. Some of my methods included:
- Googling the book title and combing through images for a high quality spine shot
- Searching for the book on ebay. Sometime people provided a spine image there, a few times I actually messaged the seller and requested a picture of the book spine.
- Emailing the publisher/author. For a few more obscure or self-published books, I couldn't find anything anywhere. But most of those small self-published authors are kind enough to actually respond to my emails and provided full-spine spreads.
- Finding a library in the US that had the book in stock and emailing a librarian to take a picture for me.

I tried everything, and eventually I managed to get spine images for every book I read in 2021, thanks in part to the kindness of strangers.

I also needed to collect the dimensions of the book, but that came fairly easily, as Amazon provides that publically for almost every physical book they sell, although there was some inaccuracies there that we'll run into shortly.

Okay! Now we have all the images and data we need, we can write the program to put them on the shelf for us.

Let's instantiate a new bookshelf object:

```
bookshelf = Bookshelf(bookshelfFileName = "example/bookshelf1.jpg", 
                      shelfWidthInches = 35.5, 
                      shelfWidthPixels = 1688, 
                      shelfBottoms = [676, 1328, 2008, 2708, 3542], 
                      shelfLeft = 75)
```

Put together the data we need in a list of objects. The shelf will be filled in the order of the list, so any sorting we may want to do needs to be done before we pass the list in. The only data the bookshelf itself needs is the "fileName", and the "dimensions" in inches.
```
exampleBooks = [
  {"title" : "Slaughterhouse-Five", "fileName" : "slaughterhouse_five-9780440180296.png", "dimensions" : "5.3 x 0.6 x 8"},
  {"title" : "Alice Knott", "fileName" : "alice_knott-9780525535218.png", "dimensions" : "6.15x1.08x9.26"},
  {"title" : "Dark Matter", "fileName" : "dark_matter-9781101904220.png", "dimensions" : "6.35x1.2x9.5"},
]
```

Fill the shelf, and save the created shelf to a file.
```
bookshelf.fillShelf(exampleBooks)
bookshelf.saveShelf("exampleShelf.png")
```

Viola! My results:
{{<figure src="https://i.redd.it/2wvq9iqpqth81.png" class="center">}}

I also added a provision in case you have more books than can fit on the shelves: if it's overfilled, a new bookshelf is created and stitched to the right of the existing shelf. Theoretically it could extend on forever, but it's versatile enough to handle anyone's annual reading list.