
# Anti-phishing

### 1. Introduction
Nowadays, our lives could not do without Internet. As the technology advances, a lot of problems regarding the convenience that the Internet brings have popped up. One of the prevailng problems is the emergence of phishing websites, which could simply be a disturbing site or a fatal zone that makes a person bankrupt through a night. Therefore, if there is a way to distinguish those phishing websites from safe ones, people can surf the Internet with more relief. Combining the technique in machine learning with such a useful topic, I would say that a safer online environment is foreseealbe.
* Objectives
    * Organize some features of general websites
    * Build models that learn from these inputs
    * Train models so that they could tell phishing websites from normal ones

### 2. Data Collection
* https://archive.ics.uci.edu/ml/datasets/phishing+websites
* Data format
    * 11055 instances
    * 30 columns
        * The first 29 columns are features
        * The last column is the label

* Attributes
1. **Use IP address as URL domain name**: if IP address is used as a domain name, it is trying to steal users' personal data, and it is probably a phishing website
    > -1 : yes
    >  1 : no

2. **URL length**: phishing websites tend to use long URL name to hide the suspicious part
    > -1 : length > 75
    > 0 : 54 <= length <= 75
    > 1 : length < 54

3. **Use URL shortening services**: this is a method on the "World Wide Web" in which a URL name can be shortened but still lead to the required page, so phishing websites could use this method and redirect users to the websites that have a long URL name
    > 1 : yes
    > -1 : no

4. **Have "@" symbol in URL**: @ symbol in a URL leads the browser to ignore everything preceding @, by adding such a symbol, phishing websites can have a legitimate URL name before @ symbol but a fake one after it
    > 1 : yes
    > -1 : no

5. **Redirecting using "//"**: // symbol in a URL means that users will be redirected to another website, so phishing websites can put their website name after //, and put a legitimate name before it, and if it is a normal // symbol, which appears after HTTP:  or HTTPS:, and the position of this symbol will be 6 or 7
    > -1 : the position of the last occurrence of "//" in the URL > 7
    > 1 : otherwise

6. **Add prefix or suffix separated by "-" to the domain**: phishers tend to add prefixes of suffixes separated by "-" so it looks like a legitimate website
    > -1 : yes
    > 1 : no

7. **Dots count in domain part**: take the URL name of our school for example, https://www.nycu.edu.tw/, first, the "www." part will be omitted, and then there's the country-code top-level domains (ccTLD) part, in this case is ".tw", which stands for Taiwan, and the remaining part is nycu.edu, the "edu" is an abbreviation for "education", and "nycu" is the actual domain name, and by eliminating the first two parts, the remaining dots count is 1, therefore, if the dots count is 2, the websites might be suspicious, and those more than 2 are probably phishing websites
    > -1 : dot count = 1
    > 0 : dot count = 2
    > 1 : otherwise

8. **HTTPS (Hyper Text Transfer Protocol with Secure Sockets Layer)**: according to the authors in ((Mohammad, Thabtah and McCluskey 2012) (Mohammad, Thabtah and McCluskey 2013), besides checking for HTTPS, which is the basic impression of a legitimate website, the trust certificate issuer and the certificate age are also key points. There are some trustworthy names of Certificate Authorities, such as "GeoTrust, GoDaddy, Network Solutions, Thawte, Comodo, Doster and VeriSign", and the minimum age of a reputable certificate is two years
    > -1 : use HTTPS && issuer is trusted && age of certificate > 1 year
    > 1 : use HTTPS and issuer is not trusted
    > 0 : else

9. **Domain registration length**: phishing websites often live for a short period of time, and most trustworthy domains are regularly paid for several years in advance
    > -1 : domain expires on <= 1 year
    > 1 : otherwise

10. **Favicon**: a favicon is a graphic image (icon) associated with a specific webpage, it is considered phishing attempt if the favicon is loaded from a domain other than the one show in address bar
    > 1 : favicon loaded from external domain
    > -1 : otherwise

11. **Use non-standard port**: if a server opens ports more than it needs, it is possible that it phishes users' information, standard ports are shown below
    > 1 : uses ports more than it needs
    > -1 : otherwise

    |PORT|SERVICE|MEANING|
    | :-----: | :-----: | :-----: |
    |80| HTTP |Hyper text transfer protocol|
    |443| HTTPS |Hyper text transfer protocol secured|


12. **The existence of "HTTPS" in the domain part of URL**: phishers may add "HTTPS" in URL to trick users
    > -1 : use "https" in URL
    > 1 : otherwise

13. **Request URL**: request URL examines whether the external objects are loaeded from another domain since most objects embedded within legitimate webpages are from the same domain
    > 1 : percentage of request URL < 22%
    > 0 : 22% <= percentage of request URL <= 61%
    > -1 : else

14. **URL of anchor**: an anchor is an element defined by the < a > tag
    1. < a > tags and websites have different domain names, this is same as request URL
    2. the anchor does not link to any webpage
<br>
    > -1 : percentage of URL of anchor < 31%
    > 0 : 31% <= percentage of URL of anchor <= 67%
    > 1 : otherwise

15. **Links in < Meta >, < Script > and < Link >**: it is expected that links in these tags of legitimate webpages are linked to the same domain
    
    > 1 : percentage < 17%
    > -1 : 17% <= percentage <= 81%
    > 0 : otherwise
    
16. **Server Form Handler (SFH)**:
    1. if SFHs contain an empty string or "about:blank", they are suspicious because an action should be taken upon the submitted form
    2. if the domain name in SFHs is different from the domain name of the webpage, it is suspicious because the submitted information is rarely handled by external domains
    <br>
    > -1 : SFH is "about:blank" or empty
    > 1 : SFH refers to a different domain
    > 0 : otherwise

17. **Submitting information to email**: phishers could use some functions, such as server-side script language "mail()" or client-side function "mailto", so that users' information might be redirected to phishers' personal emails
    > -1 : use "mail()" or "mailto" functions to submit users' information
    > 1 : otherwise

18. **Abnormal URL**: the identity of a legitimate website is typically part of its URL
    > -1 : the host name is not included in its URL
    > 1 : otherwise
    
19. **Website forwarding**: this feature is about how many times a website has been redirected, most legitimate websites have been directed only one time, and phishing websites have mostly been redirected more than four times
    > 0 : redirected times < 4
    > 1 : redirected times >= 4

20. **Status bar customization**: phishers may use JavaScript to show a fake URL in the status bar to users, so the feature checks particularly the "onMouseOver" event to see if it makes changes on status bar
    > 1 : onMouseOver changes status bar
    > -1 : otherwise

21. **Disabling right click**: phishers might disable right click function so that users cannot view source code
    > 1 : right click disabled
    > -1 : otherwise

22. **Use pop-up window**: legitimate webpages generally will not ask users to submit their personal information through pop-up window
    > 1 : pop-up window contains text fields
    > -1 : otherwise

23. **IFrame redirection**: IFrame is an HTML tag used to display an additional webpage into one that is currently shown, and phishers can use the tag an make it invisible
    > 1 : use IFrame
    > -1 : otherwise

24. **Age of domain**: most phishing websites live for a short period of time
    > -1 : age >= 6 months
    > 1 : otherwise

25. **DNS record**: if DNS record is empty or not found, the website is classified as phishing
    > -1 : no DNS record for the domain
    > 1 : otherwise

26. **Website traffic**: this feature measures the popularity of the website. Since phishing websites live for a short period of time, they may not be recognized by Alexa database
    > -1 : website rank < 100000
    > 0 : website rank > 100000
    > 1 : otherwise

27. **PageRank**: PageRank is a value ranging from '0' to '1', it aims to measure how important a webpage is on the Internet, most phishing webpages have no PageRank or less than 0.2
    > -1 : PageRank < 0.2
    > 1 : otherwise

28. **Google index**: if a website is indexed by Google, it can be shown on search results, and phishing websites may not be indexed by Google
    > 1 : indexed by Google
    > -1 : otherwise

29. **Number of links pointing to page**: the number of links pointing to the webpage indicates its legitimacy level, and legitimate webpages have at least two external links pointing to them
    > 1 : the number of links pointing to the webpage = 0
    > 0 : 0 < the number of links pointing to the webpage <= 2
    > -1 : otherwise


### 3. Prepocessing

Before starting, I preprocessed the dataset in order to obtain features of most importance to the decision of labels. By doing so, I could decrease the size of dataset and enhance the efficiency or performance of models. There are three ways to achieve this:
* depend on their coefficients
* top nodes of a tree-based model
* PCA

<font color="deepskyblue"> Coefficients </font>

Below is a form with coefficients, which are obtained by implementing <font color="red">logistic regression</font> on dataset, of each feature. With the number being bigger, either on positive or negative direction, the feature has greater influence on label classification.

![](https://i.imgur.com/tftChd4.png)
![](https://i.imgur.com/Tti30cm.png)

By keeping the top several features, the model performs as well as it processes a full size of dataset, and the best number of features to keep is around 20, which means almost one-third of features are not necessary for the model’s performance.

![](https://i.imgur.com/P9aFluM.png)

The model is Naive Bayes with Laplace smoothing.

<font color="deepskyblue"> Decision nodes </font>

Construct a decision tree model, and after training is done, plotting the tree can help visualize the decision path and feature selection on each node.

![](https://i.imgur.com/H33upxC.jpg)

Clearly, there are two features, <font color="red">SSLfinal_State</font> and <font color="red">URL_of_Anchor</font>, being top choices for decision, which are also of great importance when doing regression. The accuracy is about 0.9563.

<font color="deepskyblue"> PCA </font>

By doing PCA in order to transform dataset with <font color="red">95% variance</font>, the dimension of data is reduced to 21, and after implementing SVC model on this new dataset, the accuracy is almost the same.

1. Scale the value of all features to the interval [0, 1]
2. Do PCA with sklearn package on these scaled values and set the component parameter to 0.95
3. By transforming the dataset, the dimension of data is compressed to 21, under 95% variance
4. Implement SVC on scaled data, compare the results

### 4. Models

<font color="deepskyblue"> Naive Bayes </font>

Build a Naive Bayes model with Laplace smoothing where alpha’s value is 1. I used sklearn CategoricalNB() to implement this model, since it only accepts the data type of non negative integer, and the value of my dataset is either 0, 1, or -1, so I add 1 to each features.

<font color="deepskyblue"> SVC </font>

Implement support vector machine by using sklearn SVC module. Trying with different types of kernel, I found that the ‘poly’ one comes with the best accuracy, so such a kernel is chosen.

<font color="deepskyblue"> Decision tree </font>

Implement decision tree by using sklearn DecisionTreeClassifier module, and the model split the instances at each node with the feature that reaches the largest information gain, which is calculated by subtracting the entropy of the child node from the entropy of the parent node.

<font color="#FBB117"> Entropy formula </font>

$E(T) = -(\sum_{i=1}^c p_i \log(p_i))$

> <font color="red">T</font> is the target, phishing website or not, in this case, and <font color="red">c</font> is the total cases of the target, which is 2 in this target.

<font color="#FBB117"> Information gain formula </font>

$IG(T, A) = E(T) - \sum_{v \in A} \frac{|T_v|}{T} \cdot E(T, A)$

> <font color="red">A</font> is the attribute, feature, used to split instances, <font color="red">v</font> is all possible values in the feature.

The larger the information gain is, the smaller the entropy has become, and the splitting of the node will depend on this attribute.

### 5. Results

<font color="deepskyblue"> Naive Bayes </font>

| Accuracy | Precision | Recall |
| :---: | :---: | :---: |
| 0.9261380765752185 | 0.9368421052631579 | 0.8959731543624161 |

* confusion matrix
![](https://i.imgur.com/ZCDgEqu.jpg)

<font color="deepskyblue"> SVC </font>

| Accuracy | Precision | Recall |
| :---: | :---: | :---: |
| 0.9514621646065722 | 0.95922598479613 | 0.9315436241610738 |

* confusion matrix
![](https://i.imgur.com/7bUO89M.jpg)

<font color="deepskyblue"> Decision tree </font>

| Accuracy | Precision | Recall |
| :---: | :---: | :---: |
| 0.9571902321374737 | 0.9578804347826086 | 0.9463087248322147 |

* confusion matrix
![](https://i.imgur.com/GsSAHQd.jpg)

### 6. Conclusion
For Internet surfing safety, it is important to choose a model with great performance, and there are some key factors to analyze the performance of a model:

* Accuracy

    Absolutely, accuracy is crucial for choosing a model since it is really practical if such a model could help users inspect a website in advance. The more accurate a model can tell phishing websites from legitimate ones, the more security of surfing Internet can be assured.

* Precision

    There are two cases of precision could be considered, however, the case of “Legitimate” could be a bit more important. The reason for this is because with higher precision of “Legitimate” websites, few phishing websites, which bring real danger to users’ privacy and security, are classified as “Legitimate”, so users are less possible to enter a webpage that is said to be legitimate but actually a dangerous phishing webpage.

* Recall

    For recall, it is more important to check “Phishing” case in order to understand what proportion of those dangerous phishing websites are picked out. If the recall of “Phishing” is relatively low, it means that relatively more phishing websites are said to be legitimate, and it leads to a higher risk of users being phished.
