
Setup
As always, all the code is on GitHub.

To make things really simple, our extension will not actually have a pop-up (the little window that appears when you click an extension’s icon) — it will simply be working in the background.

As such, all we really need are 3 files. These are:manifest.json , background.js and linkedin.js .

Thus, Step 1 is: Make an empty repository and create those files inside.

Now, let’s go through their content.

manifest.json
manifest.json is the only essential file an extension needs. In our case, it will contain metadata about the extension, the permissions it needs to operate, and the script it should run in the background. Here’s what it looks like:


Besides the metadata, here’s a bit of info on the important things this manifest is setting up for us:

Permissions

permissions establish what your extension is allowed to do, such as getting the URL of the current page, or adding JavaScript to a website.

When you’re publishing your extension somewhere like the Chrome Web Store, you’ll be asked to justify each permission you’re requesting access to, in order to ensure the security and privacy of your users.

Here, we’re asking for permissions for tabs and webNavigation in order to know when we’re visiting a new website, as well as what that website is. We need this to check if the website is LinkedIn so we can launch our adblocking script.

Lastly, we ask for permission to modify the https://www.linkedin.com/ webpage, so that we can insert our script.

Background

background specifies scripts that should run when certain events are triggered. A key event most extensions listen for in some way is the loading of a new webpage.

In our case, we’ll be listening for navigation changes and getting the URL of each new requested website.

background.js
Moving on, let’s get into some juicier stuff.

In background.js , we’ll be setting up our event listener that will trigger an action every time the user loads a new webpage. We’ll then check if that website is LinkedIn, and if it is, we’ll block ads.

Here’s the content of the file:


Since I left some comments in the file, I won’t go too deep into what each line is doing.

But, in essence, what we are doing here is getting the URL when a new page loads, stripping the URL of unnecessary stuff and checking if the domain itself actually matches linkedin.com.

If it does, we inject the contents of linkedin.js into the website, which will work to remove our ads. If it doesn’t, we just don’t do anything.

linkedin.js
The last file we have left specifies how to actually block the ads.

For this, I’ve done a little bit of homework so that you don’t have to.


The picture on the left is a screenshot of my LinkedIn feed being inspected with the browser’s Dev Tools.

You can see that, since ads have to be flagged appropriately, LinkedIn adds a line that says “Promoted” to the post in order to indicate that it is an ad.

However, unlike Facebook, LinkedIn doesn’t really try to obfuscate HTML to make it hard to distinguish ads, so simply looking through the page for elements that contain the word “Promoted” in them is all you need to find an ad in JS.

Then, to actually remove the ad, we need to find the ancestor (n-th parent) element that wraps around the entire post and get rid of that. With Dev Tools, you can actually right-click on the element and select “Remove Element” to see the result. In our code, we are going to set that element’s style attribute to display: none; , which does the trick.

So here’s what that looks like:


In short, we iterate over all span elements looking for those that have the “Promoted” text, try to get the div that wraps around the ad in two different ways, and then get rid of it by setting display: none; .

To find the right ancestor, we first use a built-in method for HTML elements called closest . This will iterate over the ancestors of our span until it finds the first one with class feed-shared-update-v2 . I identified that as the element we need to “delete”.

However, if LinkedIn were to update their UI and change that class to feed-shared-update-v3 , the method above won’t work. As such, we then try to get the 6th parent, which should be equivalent to the same div from the other method.

Naturally, if LinkedIn changes that class and the structure of their promoted posts, our extension will probably fail. But for a while at least, it should work just fine.

We also use a non-sophisticated way to ensure ads that are loaded dynamically as the user scrolls also get deleted, by using setInterval to run the script every 100ms.

Generally, when I use this extension, I sometimes see the first ad for a brief moment before it disappears, but then never see a hint of another ad when scrolling.

Adding the extension to your browser

My Brave Browser Extensions Page
So that’s all for the extension. The whole thing is about 100 lines of code, with comments.

As for how you use it, here’s what you do:

Click your browser’s “burger menu” on the top right corner or visit Settings directly
Navigate to ‘Extensions’
Toggle on “Developer Mode”, which is usually on the top right corner of the ‘Extensions Page’
Some buttons should have appeared on the top of the page. Click “Load Unpacked”
Select your project directory from the File Browser
You’re all set!
If you’ve followed all the steps correctly, you should now have a new icon where you extensions are located, also on the top right.

Since we didn’t set an icon for it, it’s probably going to be an ‘L’ inside of a square.

Now go ahead and test it out! Here’s a handy link to LinkedIn.

Wrapping Up
And that’s all! I really believe this is all doable in 10 minutes.

Of course, the goal here was to build something useful very quickly, so we sacrificed a few aspects such as having an actual pop-up or even an icon.

However, this shows that by simply sparing a coffee break, you can actually build useful software for yourself (and maybe even others).

Finally, if there’s any interest in exploring browser extensions further, do let me know and I’ll look into going more in-depth next time.

Thanks for reading and have fun scrolling LinkedIn… If that’s even a thing.


