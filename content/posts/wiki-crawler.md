---
title: "Random Walkers and Web Crawlers"
draft: false
tags: ["projects","quickie", "web crawling", "random walks", "random graphs"]
date: 2019-09-02T17:51:49-04:00
---

**Brief Overview:** This was put together for a class assignment in Probability Theory 2. We were to discuss random walks and how they relate to the [PageRank algorithm](https://en.wikipedia.org/wiki/PageRank). 

An overview of the relevant mathematics is given, and a small web crawler is implemented to collect links between wikipedia articles. The links are saved in a SQLite database and used later to construct a graph and rank articles based on centrality. 

If you're interested in the code and results, most of that is at the bottom of the article. We begin with a simple overview of the concepts of graphs, centrality, and random walks. 

**Page Ranking Overview**
At it’s core, Google’s PageRank algorithm is an application of fundamental mathematical techniques. It combines techniques from several disparate but related fields: the theory of graphs and random graphs, linear algebra, probability, statistics, and computer science.

The world wide web is an enormous network, the scale of which few human inventions can match. This fundamental fact about the internet plays an important role in understanding the PageRank algorithm. Each web page on the internet can be thought of as a node on a giant graph, where each edge is a link between two web pages: when one page links to another using a hyperlink, that can be thought of as another edge on the graph.

The PageRank algorithm assigns a rank to a webpage based on its “centrality”. In essence, the algorithm returns the likelihood of a user arriving at a given page were they simply making random jumps from one web page to another. 

**Random Graphs and Centrality**

A *graph* is a pair of sets *G = {V, E}*, where V is a set of vertices (or nodes) and E is a set of ordered pairs Ei,j = {Vi, Vj} that represent edges connecting those vertices. A random graph is one in which the edges, the nodes, or both are determined probabilistically. 

One of the simplest measures of centrality is that of degree centrality. The degree of a node is equal to the number of edges attached to it. On a directed graph, such as the kind we’re concerned with, the degree centrality can be measured in terms of indegree (the number of edges coming into the node) and outdegree (the number of edges coming out of the node). When looking at degree centrality, the nodes with the highest number of edges are the ones that are “most central”. 

Another measure of centrality is closeness centrality. We are often concerned with paths on graphs. A path is a sequence {p1, l1, p2, l2, …,pk-1, lk-1, pk} of nodes and edges, where each edge connects the previous and the next node. In examining closeness centrality, we are only concerned with paths that do not have cycles or loops. These are equivalent to self-avoiding walks on a graph. To measure closeness centrality, we examine the average distance from a node to all other nodes on the graph. The nodes with the shortest average distance are the most central.

An important measure of centrality for our study of PageRank is eigenvector centrality. Eigenvector centrality utilizes the adjacency matrix of a given network (the *n x n* matrix with *1*s indicating a connection between nodes *i* and *j* and *0*s indicating no connection). The eigenvector centrality score of a given vertex *i* is the numerical score at index *i* of the eigenvector solution to the adjacency matrix. 

There exist many other measures of centrality, such as betweenness centrality, harmonic centrality, percolation centrality, and so on. 

PageRank centrality is a modification of Katz Centrality. In Katz centrality, the centrality of node i, *C(i)* is equal to: 

`$ C(i) = \sum_{k=1}^{\inf}\sum_{j=1}^{n}(\alpha^{k} A^{k}_{ij})$`

Here, `$\alpha$` is known as the *damping factor*. *A* is the adjacency matrix for the graph in question, with each power *k* denoting a matrix whose cells indicate a connection between nodes *i* and *j* through *k* linking nodes. 

This has a convenient matrix formulation:
`$C  = ((I - \alpha A^{T})^{-1} - I)J_{n,1} $`

Here, *I* is the identity matrix and *J* is a vector of 1s. 

Further details are given in the original Katz paper *"A New Status Index Derived From Sociometric Analysis"*, which can be found [here](http://people.cs.vt.edu/badityap/classes/cs6604-Fall13/readings/katz-1953.pdf). Overviews of other centrality measures can be found [here](http://people.cs.vt.edu/badityap/classes/cs6604-Fall13/student-lectures/centrality-measures.pdf) and [here](http://pages.di.unipi.it/marino/centrality18.pdf).

PageRank modifies the Katz Centrality measure by acknowledging that some nodes may link to other nodes prolifically and that this can adversely affect measures of centrality. PageRank remedies this by utilizing an iterative algorithm that initializes all nodes to a rank equal to *1/n*, and then in each iteration having each node donate some measure of its own centrality to the nodes it links to in equal proportion. As a result, pages that link to many other pages will donate only small quantities of centrality to the nodes they link to. 

**Random Walks and Markov Chains**

Any measure of centrality can be normalized (if it is not already). Doing so generates a probability distribution over the nodes in the network, which can be interpreted as the long term probability of a "random walker" arriving at that node.

A random walker is a form of *Markov chain*. A Markov chain is a stochastic process - or, series of random variables - `${X_{n}:n \in N}$` such that `$P(X_{t} = j | X_{0} = i_{0}, X_{1} = i_{1}, ..., X_{t-1} = i_{t-1}) = P(X_{t} = j | X_{t-1} = i_{t-1})$`. 

That is, a Markov chain is a system whose state is dependent only on the immediately preceding state, ignorant of the past of the system. This *memoryless property* is also known as the *Markov property*. 

A random web surfer can be thought of as a Markov chain whose states are webpages and whose transitions are hyperlinks away from the present webpage, where each transition is equally probable. The damping factor represents a probability of the process halting at any state.

A result of this is that the PageRank of a webpage determined by this algorithm is convergently equivalent to the probability of arriving at that page as the number of page transitions approaches infinity. This is a result of the **Fundamental Theorem of Markov Chains:**

(Fundamental Theorem of Markov Chains) For a connected Markov chain (one in which every vertex or state is reachable from every other vertex or state) there exists a unique probability vector `$\pi$` satisfying `$\pi P = \pi$`, where *P* is the transition probability matrix for the Markov chain. Further, for any starting distribution, `$lim_{t -> inf}a^{(t)} = \pi$`

The unique probability vector `$\pi$` is known as the *stationary distribution* for the random walk, and is the set of values that the PageRank algorithm will converge to over time assuming no changes in the underlying set of pages.

**The Wikipedia Webcrawler:**

This is a very simple web crawler. I'm not super familiar with web crawling, but there's several good posts that I referenced while working, including [this one](https://www.codeproject.com/Articles/1087859/Web-crawling-with-Csharp-part-one-8). I used the [HTMLAgilityPack](https://html-agility-pack.net/) library to do much of the heavy lifting - it provides methods for loading web pages and selecting content by ID, class, et cetera. 

Note that the crawler doesn't limit how quickly it accesses Wikipedia. On my end, it runs relatively slowly due to all of the link parsing and database accessing. If you're doing quick work, you might want to throttle your web crawler.

Code for the crawler is below:

{{<highlight csharp "linenos=table">}}
using System;
using System.Collections.Generic;
using HtmlAgilityPack;
using System.Data.SQLite;
using System.Linq;

namespace WikiCrawl
{
    class Parser
    {
        static void Main(string[] args)
        {
            // Create database
            /*
            SQLiteConnection.CreateFile("WikiLinks.sqlite");
            SQLiteConnection wikidb = new SQLiteConnection("Data Source=WikiLinks.sqlite");
            wikidb.Open();
            SQLiteCommand createLinkTable = new SQLiteCommand("CREATE TABLE WikiLinks (link TEXT PRIMARY KEY, title TEXT, crawled INTEGER)", wikidb);
            SQLiteCommand createEdgeTable = new SQLiteCommand("CREATE TABLE WikiGraph (fromLink TEXT, toLink TEXT)", wikidb);
            createLinkTable.ExecuteNonQuery();
            createEdgeTable.ExecuteNonQuery();
            wikidb.Close();
            */
            // Populate from initial page
            //readPage("https://en.wikipedia.org/wiki/Kolmogorov_complexity", wikidb);

            SQLiteConnection wikidb = new SQLiteConnection("Data Source=WikiLinks.sqlite");
            // Iterate through links in database until told to stop
            int exit = 0;
            while (exit == 0)
            {
                wikidb.Open();
                SQLiteCommand peruseTable = new SQLiteCommand("SELECT * from WikiLinks WHERE crawled = 0 LIMIT 100", wikidb);
                SQLiteDataReader tableReader = peruseTable.ExecuteReader();
                var uncrawled = new List<string>();
                while (tableReader.Read())
                {
                    uncrawled.Add(tableReader["link"].ToString());
                }
                wikidb.Close();
                foreach (string link in uncrawled)
                {
                    readPage(link, wikidb);
                    SQLiteCommand updateLink = new SQLiteCommand(string.Format("UPDATE WikiLinks SET crawled = 1 WHERE link = \"{0}\"", link), wikidb);
                    wikidb.Open();
                    updateLink.ExecuteNonQuery();
                    wikidb.Close();
                }
                string check = Console.ReadLine();
                if(check == "exit")
                {
                    exit = 1;
                }
            }
        }
        static void readPage(string link, SQLiteConnection db)
        {
            // Selects the content div of the article after loading the page
            string[] disallowed = { "Help:", "Special:", "Wikipedia:", "Portal:", "Talk:", "Category:", "Book:", "Template:", "Template_talk:" };
            HtmlWeb web = new HtmlWeb();
            HtmlDocument page = web.Load(link);
            HtmlNode contentDiv = page.DocumentNode.SelectSingleNode("//div[@class='mw-parser-output']");
            db.Open();
            // Iterate through every link in the content div
            foreach (HtmlNode href in contentDiv.SelectNodes("//a"))
            {
                string linkVal = href.GetAttributeValue("href", null);
                string linkTitle = href.GetAttributeValue("title", null);
                string linkClass = href.GetAttributeValue("class", null);
                // Exclude empty links, links from the disallowed list, and links that leave wikipedia
                if (!String.IsNullOrEmpty(linkVal) &&
                    String.IsNullOrEmpty(linkClass) &&
                    linkVal.IndexOf("/wiki/") == 0 &&
                    disallowed.Any(linkVal.Contains) == false){

                    // Does some additional link checking...
                    // The # symbol indicates a subsection of an article. We don't want to treat these as separate from the original article,
                    // so we remove the hash and everything after it. 
                    int indOfHash = linkVal.IndexOf('#');
                    if (indOfHash != -1)
                    {
                        linkVal = linkVal.Substring(0, indOfHash);
                    }

                    // Tries to add link to the edge table and crawl table, catches failures
                    linkVal = "https://en.wikipedia.org" + linkVal;
                    
                    try
                    {
                        string edgeCommand = string.Format("insert into WikiGraph (fromLink, toLink) values (\"{0}\", \"{1}\" )", link, linkVal);
                        SQLiteCommand addEdge = new SQLiteCommand(edgeCommand, db);
                        addEdge.ExecuteNonQuery(); 
                    } catch
                    {
                        //Console.WriteLine("Edge already exists");
                    }
                    try
                    {
                        string linkCommand = string.Format("insert into WikiLinks (link, title, crawled) values (\"{0}\", \"{1}\", \"{2}\")", linkVal, linkTitle, 0);
                        SQLiteCommand addLink = new SQLiteCommand(linkCommand, db);
                        addLink.ExecuteNonQuery();
                    } catch
                    {
                        //Console.WriteLine("Link already exists");
                    }
                }
            }
            db.Close();
        }
    }
}
{{</highlight>}}

**Page Ranking:**

Using the data collected from running the web crawler for a short while, we're now gonna do some page ranking. When I ran it, I collected 67,672 records: articles linking to other articles. This was from crawling a total of 348 articles. I parsed the records down to just the links between pages that were crawled, resulting in 19,577 records total. This parsing was done with the following SQL:

{{<highlight SQL "linenos=table">}}
CREATE TABLE subGraph AS SELECT * FROM WikiGraph WHERE (fromLink in (SELECT entry.link FROM WikiLinks entry WHERE entry.crawled = 1)) AND (toLink in (SELECT entry.link from WikiLinks entry WHERE entry.crawled = 1));
{{</highlight>}}

With python, we can get the degree centrality of each node using the [NetworkX](https://networkx.github.io/) library and write it to a file. This can give us a quick, rough approximation for the rankings of our webpages.

{{<highlight python "linenos=table">}}
import csv
import networkx as nx 
import sqlite3 

wikiconn = sqlite3.connect('WikiLinks.sqlite')
graphlist = list()
for row in wikiconn.execute('SELECT * FROM subGraph'):
	graphlist.append(row)
wikig = nx.MultiDiGraph()
wikig.add_edges_from(graphlist)
centrality = nx.degree_centrality(wikig)
with open('centrality.csv', 'w') as file:
	for site in centrality.keys():
		file.write("\""+site+"\"," + str(centrality[site]) + "\n")

{{</highlight>}}


Below, we implement the simplified PageRank algorithm ourselves in C#:
**In progress**


**To Do Still:**

- Implement simplified PageRank iterative algorithm in C#
- Implement PageRank using MCMC (see: [this paper](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.59.136&rep=rep1&type=pdf) )



