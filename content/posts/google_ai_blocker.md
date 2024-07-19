+++
title="Blocking Google's AI Search Results"
date = "2024-06-14"
+++

I haven't been a fan of a lot of the new AI "features" various companies have been rolling out. It seems like it's a big push culturally, but I don't think the tech is quite there yet. It's impressive, don't get me wrong, but I've often found it hampering my productivity and getting in my way more than its helped me.

I'd never made a Chrome extension before, so I figured now would be a good time to learn. Really, it was pretty simple. I analyzed the Google AI search results construction. Google randomizes the ids on their divs, so it wouldn't be quite that easy to grab what I wanted to block. Luckily, they always have the same structure. So I wrote a script to look for the  term "AI Overview", and then hide the parent node of that. Technically, the AI results are still loaded, but the user won't see them. The whole script was around twenty lines of code.

The only issue with this approach is if they change the term to "AI Summary," or reorganize their divs, it's going to need a small manual refactor.

I went ahead and published it on the chrome webstore, which was honestly more of a headache than writing the extension itself. Also, they made me pay five bucks for the privlidge. If you'd like to take a look at the code, it's [open-sourced here](https://github.com/nuffertaylor/google-ai-blocker). And if you'd like to add the extension to enhance your google experience, [you can download it here.](https://chromewebstore.google.com/detail/google-ai-results-blocker/cnnlcgcbchhbgagkiaphfoiglddejnbh)