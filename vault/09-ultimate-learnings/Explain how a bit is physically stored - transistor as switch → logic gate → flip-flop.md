
প্রথমে বাংলায় বলার চেষ্টা করিঃ
Bit বলতে আমরা মূলত 0 বা 1 কে বুঝে থাকলেও আসলে যেকোনো ডিজিট্যাল ইলেকট্রনিক মেশিনে 0 আর 1 দিয়ে নির্দিষ্ট কিছু ভোল্টেজ রেইঞ্জকেই বুঝায়। যেমনঃ 0 হলো 0-0.8 volt, 1 হলো 2-5 volt (এরকম ধরে নেওয়ার কারণ জানিনা, এমনকি ভোল্টেজ কোনোভাবে এর মাঝামাঝিতে চলে গেলে কি হতে পারে তাও জানিনা।) 
এখন, ট্রানজিস্টরই সেই যন্ত্র যেটা সুইচ হিসেবে কাজ করে ইলেকট্রন প্রবাহ চলতে দিতে বা রুখে দিতে পারে। অর্থাৎ ট্রানজিস্টর মূলত তিনটি অংশ দিয়ে তৈরি: PNP অথবা NPN।
এখানের প্রথম অংশটি সম্মুখী বায়াস এবং পরের অংশটি বিমুখী বায়াসে থাকে।
এমন হলে কী হয়? অল্প পরিমাণ কারেন্ট যদি ইনপুটে দেওয়া হয় বা সম্মুখী ঝোঁক দেওয়া হয়, তাহলে সেটা বিমুখী ঝোঁকের বাধা পার করে আউটপুটে ভোল্টেজ তৈরি করতে পারে।
সংক্ষেপে বলতে গেলে, ট্রানজিস্টর এভাবেই সুইচ হিসেবে কাজ করে।

এরকম ট্রানজিস্টর বিভিন্ন কম্বিনেশন এ সাজিয়ে মূলত এক একটা logic gate তৈরি করা হয়।

এমন logic gate যদি আমরা এমন কম্বিনেশন এ সাজাই যেখানে input এর কোনো একটা তার output এর সাথেও যুক্ত থাকে, তবে সেটা মূলত একটা memory component হিসেবে কাজ করতে পারে। অর্থাৎ, ধরুন কোন input দিলাম 1। এখন সেটার output ও হতে পারে 1। এবং যেহেতু output আর input একে অন্যের সাথে connected, এটা যেকোনো একটি input আবার one হিসেবে fetch করবে।
এই ক্ষেত্রে output এর সাথে input connected থাকার কারণে এটা Data store করে রাখার ক্ষমতা রাখে।

আর flip flop হলো logic gate দিয়ে তৈরি সবচেয়ে ছোট component, যেটা একটি bit data ধরে রাখতে পারে। অর্থাৎ, এর মধ্যে আমরা কোন voltage না থাকা অথবা কিছু voltage থাকা বা one-এর মতো state permanently ধরে রাখতে পারি।

মূলত Flip-flop এ কয়েক ধরনের Logic gate এর কম্বিনেশন use করা হয়। এভাবে মূলত computer যেকোনো bit physically voltage আকারে transistor এর মাধ্যমে, logic gate এর মাধ্যমে এবং মোট কথা বড় আকারে দেখতে গেলে flip-flop এর মাধ্যমে component এর মধ্যে store করে রাখতে পারে।

In English:
We usually think of 0 and 1 as bits in digital electronic machines, but what we actually mean is specific voltage ranges. For example, 0 is typically 0 to 0.8 V. I do not know why it is defined that way, and I also do not know what happens if the voltage falls somewhere in between.

A transistor is the machine that acts as a switch and can either allow current to flow or stop it. It has three parts: PNP or NPN. In the first part of the circuit (the forward-biased part), if we apply a small voltage, it can actually A transistor works as a switch in digital electronics by overcoming the reverse-bias barrier and allowing current to pass. If we design transistors in different configurations, we can build logic gates.

If we connect a logic gate’s output to one of its input wires, it can act as a memory component. For example, if one input is set to 1, the output becomes 1, and that state is fed back to the input so it can maintain that state. That is how a flip-flop, the smallest memory storage component, is made from logic gates. A flip-flop can store one bit of data, meaning it holds a voltage or not, using combinations of NAND and OR gates, and OR gates alone. With a clock, we can turn a latch into a flip-flop. That is how a computer physically stores any bit as voltage.