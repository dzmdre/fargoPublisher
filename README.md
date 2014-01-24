### What is Fargo Publisher? 

Fargo Publisher is a node.js app that connects to <a href="http://fargo.io/">Fargo</a> to publish a folder of HTML docs.

An upcoming release of Fargo has a new built-in content management system that creates static HTML files. All that's needed is a way to flow those files to a static HTTP server. That's what Fargo Publisher does. 

It defines an open protocol that any app can use to connect to static storage, no matter where the content originates. This implementation stores the files in Amazon S3, but a fork of this project could store them in files, or in another content server. 

It's open source, using the MIT License. 

This document includes information you need to install a server, and technical information for implementers.



### How names work

Each outline can be given a public name.

Rather than invent our own naming system, we're using the Internet's -- DNS.

So if I call my outline "dave" -- you can refer to it as <a href="http://dave.smallpict.com/">dave.smallpict.com</a>.

When you go to that address, you get the rendered content. 

There's only one element of the &lt;head> section of that outline that we care about, &lt;linkHosting>. It's automatically maintained by Fargo. 

The rest of the outline contents is  up to the user.



### User experience (set up)

The Fargo user brings the outline he or she wants to make public to the front.

Choose the <i>Name Outline</i> command in the File menu.

A <a href="http://static.scripting.com/larryKing/images/2013/05/14/choosePublicName.gif">dialog</a> appears.

Type a name. As you type the software tells you whether the name is taken. It does this with a call to Fargo Publisher.

When you click OK, a message is sent to Fargo Publisher to associate that name with the public URL of the outline. Getting a public URL for a file is a feature of Dropbox.



### User experience (publishing)

When the user publishes an outline, or a portion of an outline, it could cause many files to be rendered. 

The text of those files is saved to a <i>package</i> file, which is linked into the user's outline, automatically by the CMS.

When the text is fully rendered, it sends a message to the Publisher server with the name of the outline.

Publisher then reads the outline (it was registered in the previous step) locates the package file, reads it, breaks the package up into its component files and saves them to the user's folder in the S3 bucket. 

Publisher sends back to Fargo the base URL of the folder, which is then hooked into the Eye icon, which can be used to view the headline the cursor points to.



### User experience (viewing rendered content)

The Eye icon in the left margin of Fargo is super-important.

It connects the editing context with the viewing context.

Put the cursor on any headline in a public outline, and click the <a href="http://static.scripting.com/larryKing/images/2014/01/23/theAllImportantEye.gif">Eye</a>.

A new tab opens with the rendered view of that section of the outline.



### Before deploy

You're going to need to make a few decisions before you deploy. 

1. The user files will be stored in an S3 bucket. There is an S3 path for that location. What should it be?

2. Where do you want to store the FP data? This includes information about the user's outlines, and stats generated by Fargo Publisher. It's also an S3 path. You might not want this to be public, although there is no personal information stored about the user outlines. It's all designed for publishing. These are <i>public</i> outlines, by definition. 

3. What domain for the user names? You have to own the domain and the DNS should point to the node server so it can redirect to the user's content when subdomains are accessed.

For my deployment I went with (Unix shell commands):

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. export fpHostingPath=/beta.fargo.io/users/

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. export fpDataPath=/beta.fargo.io/data/

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. export fpDomain=smallpict.com

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4. export fpServerPort=1337





### How to deploy

You must have a current node.js installation.

Install <a href="https://github.com/mikeal/request">request</a>, <a href="http://aws.amazon.com/sdkfornodejs/">AWS</a> and if necessary <a href="http://nodejs.org/api/url.html">url</a>. 

Set environment variables for AWS.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AWS_ACCESS_KEY_ID

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;AWS_SECRET_ACCESS_KEY



Set environment variables with your S3 paths and the domain you're using.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fpHostingPath -- where the users' files will be stored.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fpDataPath -- where you want names and stats to be stored. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;fpDomain -- the domain we're allocating (should be set, via DNS and AWS, where it should point is still TBD).



The app is in package.js. package.json already contains all the info that node needs to run it.



### Notes

There's a folder of OPML files that contains the outlines that the JS and MD come from.

We use the MIT License. Nothing proprietary about the protocol or the code. You are encouraged to clone, innovate, enjoy.

I love programming in this mode. The tools are great, and node is a wonderful environment, made that way by programmers who share their work so generously. The quality of the work is very impressive. And the commitment to no breakage also refreshing in this day and age. 

Thanks to Brent Simmons for his Hello World server <a href="http://inessential.com/2013/12/09/getting_started_with_node_js_for_cocoa_">example</a>. They helped this node newbie get started. He says his example is for Cocoa developers, but that's not true. I understood it, and I've never written a line of Cocoa in my life, and think it's a silly name for a programming environment. ;-)



### Todo list

I had to not use the CNAME in testing yesterday. Verify that it works.

Make the port a configurable option.

Stats!

"This type of response MUST NOT have a body. Ignoring data passed to end()."

Clean up console log. Too noisy.

When you go to dave.smallpict.com, the rendered website is displayed or the OPML. A choice must be made.

Related question -- where to point the fpDomain environment variable. Where ever it points must be ready to redirect, based on the choice we make, above.

How do we backup the database?

Be smarter about file types? -- Should HTML files really be text/plain type? (Are they?) 



### Done

User interface in Fargo.

Come up with a cname for fargopub1.jit.su. (pub.fargo.io)

Create beta.fargo.io.

Parameterize using environment variables. See <a href="http://stackoverflow.com/questions/4870328/how-to-read-environment-variable-in-node-js">howto</a>.

Determine the S3 buckets to use for the Fargo 2 beta deploy.

Import data from smallpict.com.

Fix the pingpackage call to use the name not the url of the outline.



