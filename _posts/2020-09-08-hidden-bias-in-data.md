---
  layout: post
  title: "Garbage in, Garbage out: Hidden biases in data."
  date: 2020-09-08
  author: Aanand Shekhar Roy
  cover: '/assets/img/leaning-tower.jpg'
  tags: Product
  comments: true
  post_url: hidden-bias-in-data
---

Building a data-driven product is not an unusual idea these days. Building a product using data is in itself a pretty big challenge, but what if the most important tool we are using, on which our entire strategy is based, in itself crooked? Seems unlikely? Think again. 

In this article, I’ll try to explain, with the help of certain examples, that there is more than it meets the eye with data, and how simple biases (direct, or indirect), can muddy the sample, which in turn can ruin any inference that you might be making.

# The Literary Digest fiasco

Let’s look at a very famous example, which also marked the beginning of the demise of popular magazine *The Literary Digest*.
It had 10 million telephone and digest subscribers, who concluded that in the presidential election of 1936, the Republican candidate, Governor Alfred Landon of Kansas, was likely to be the overwhelming winner, against the incumbent Franklin D. Roosevelt.

**The power of statistics is that the inferences made from reasonably large, carefully drawn samples, could be *as accurate* as the same information drawn from the entire population.**

You’d think that in the case of the magazine, the sample is humongous, so the inference, in this case, who-will-be-the-winner, should be accurate. Right? Wrong.

It was Franklin D. Roosevelt who came on top in that election. So why did this data fail?

## Why did it fail?

The ‘autopsy’ done later by a lot of thesis on this issue found the reasons.


First, the people who had the subscription didn’t represent a cross-section of the voters. Not everyone had telephones or were affluent enough to afford a magazine subscription. So the data was inherently biased. The people in the sample were a special kind of people, perhaps mostly loaded with Republicans.


While delving into the data, we have to be careful of these ‘special kinds of people’ who might be responding to your research. 

You might have heard about people “lying with statistics”, but many times, the statistical methods are sound, it’s just that the data you are feeding is garbage. 

One of the well-written books on statistics, [Naked Statistics by  Charles Wheelan](https://amzn.to/32872do) had a quote that summed it up well:


> “Most statistics books assume that you are using good data, just as a cookbook assumes
> that you are not buying rancid meat and rotten vegetables. But even the finest
> recipe isn’t going to salvage a meal that begins with spoiled ingredients”

No amount of data-size or statistical methods will make up for the inherent bias present in the data.

# Types of Biases

Some common examples of biases are:

**Apprehension bias:** This one is pretty nasty, and you’d be surprised how common it is. This type of bias stems from when a study participant responds differently due to being observed. A simple example is that patients could become anxious as a result of visiting their health professionals. They may also become anxious at the thought of having their blood pressure taken before it has been taken.  As a result, this could raise their blood pressure, giving a biased record of what their physiological blood pressure is.
You can tie it back to your user-research use-cases, where the participant might be behaving differently than they would otherwise, just because you are innocently trying to observe their behavior. (Paradox? Christopher Nolan’s next script?)


**Attrition bias:** This is usually a result of the unequal loss of participants from study groups in a trial/sample. Imagine trying to assess the impact of diets on patients with depression. But, severely depressed patients might be finding it difficult to continue the study and bailing out.


**Selection bias:** Literary digest sample had a selection bias in it. It didn’t give a good chance to non-republican voters to participate since the subscribers were mostly Republicans.
Next time when you send out any surveys, or customer research artifacts, always ask this question. ‘Does everyone in my customer segment have an equal chance to participate in this survey?’ The answer might not be straight-forward.  You go to the street-market, you bias your sample against stay-at-homes. Go door-to-door, you bias against employed people who might not be at homes a lot, or at least during office hours. (example is taken from [How to Lie with statistics](https://amzn.to/2DE5tKJ))


**Publication bias:** Positive studies are far more likely to be published than the negative ones. The source of bias isn’t the studies, but due to the skewed ratio of publication of positive findings as compared to negative findings. 
Example? Read this from The New York Times’s article [Antidepressant Studies Unpublished](https://www.nytimes.com/2008/01/17/health/17depress.html) that says:


> “The makers of antidepressants like Prozac and Paxil never published the results of about a third of the drug trials that they
> conducted to win government approval, misleading doctors and consumers about the drugs’ true effectiveness, a new analysis has
> found. In published trials, about 60 percent of people taking the drugs report significant relief from depression, compared 
> with roughly 40 percent of those on placebo pills. But when the less positive, unpublished trials are included, the advantage
> shrinks: the drugs outperform placebos, but by a modest margin, concludes the new report, which appears Thursday in The New 
> England Journal of Medicine.“



There are a lot of different kinds of biases. In fact there is a [catalog of biases](https://catalogofbias.org/biases/). 

# Takeaway

Once you know which kind of bias may be present, you can work on fixing the problem. A simple example would be, If you do door-to-door research, where your sample might be biased against employed people, you can visit them at different times, on weekends, etc where you might find the people that were generally missing from the dataset you first created.



By now, you should start appreciating these facts:

1) A representative sample is a precursor to getting correct insights into the problem you intend to solve.

2) Getting good data is harder than it looks.

3) It’s easy to make mistakes by applying good statistical methods on bad samples.

4) Size matters, a bigger sample is better, but no amount of data will make up for the harm caused by a biased sample.

If you have a truly random sample, then it’s a very good chance that it is representative of the population you are targeting. But it’s easier said than done as we have seen in the above example. The test of the random sample is very simple.


*Does everyone in the group have equal chance to be in the sample?*

---
Resources:

[How to Lie with Statistics By D. Huff,I. Geis](https://amzn.to/2DE5tKJ)

[Naked Statistics: Stripping the Dread from the Data](https://amzn.to/32872do)

[Catalog of biases](https://catalogofbias.org/biases/)

[NYTimes article Antidepressant Studies Unpublished](https://www.nytimes.com/2008/01/17/health/17depress.html)

[Wikipedia on The Literary Digest](https://en.wikipedia.org/wiki/The_Literary_Digest)