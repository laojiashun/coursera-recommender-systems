# Non-Personalized and Content-Based

**Dimensions of Analysis**
- Domain
- Purpose
- Recommendation Context
- Whose Opinions
- Personalization Level
- Privacy and Trustworthiness
- Interfaces
- Recommendation Algorithms

**Level of Personalization**
- Generic / Non-Personalized
- Everyone receives same recommendations
- Demographic
- Matches a target group
- Ephemeral (short duration)
- Matches current activity
- Persistent
- Matches long-term interests

**Types of Output**
- Predictions
- Recommendations
- Filtering
- Organic vs. explicit presentation
- Agent/Discussion Interface

**Types of Input**
- Explicit
- Implicit

**Recommendation Algorithms**
- Non-Personalized Summary Statistics
- Content-Based Filtering
- Collaborative Filtering
  - User-User
  - Item-Item
  - Dimensionality Reduction
- Hybrid

### Non-Personalized
Works well when we don't know anything about the user (cold start problem).  Simple but beneficial, efficient and fast to compute.

#### Summary Statistics
Data from the community can be used to identify best sellers, most popular and trending.  By aggregating behavior (frequency, average) we can get a sense for what the community is doing.

- consumed: most / longest visited
- popularity (can be an important metric)
- count: quantity sold or accepted
- average: rating for an item

__things to consider:__
- averages can be misleading.
- people rate on different scales
    - consider normalizing data  
- credibility of users who rate or provide feedback
- more data is better
- show more information such as count or distribution
- context is also important to consider
    - need to understand relationship between summary stat and user need. (e.g. correct average)
- personalization addresses these limitations

__damped means__  
Solves for low confidence and few ratings or scores when computing averages.

The standard mean is the sum over users $u$ the user rating for each item $r_{ui}$ and divide by the total number of items $n$.
$$
\text {mean} = s(i) = {\sum_{u} r_{ui} \over n}
$$

$k$ is the damping factor and $\mu$ us the global mean.
$$
\text {damped mean} = s(i) = {\sum_{u} r_{ui} + k\mu \over n + k}
$$

### Weak Personalization
Used when we know a little bit about the user such as zip code or current generic context (e.g. web page you are on, current item being browsed).

#### Demographics and Stereotypes
Popularity may not always represent the tastes of the target user.  Personalizes to certain attributes of the individual by breaking down summary statistics by demographic.
- Age (range)
- Gender
- Race/Ethnicity
- Socio-Economic Status
- Location

Taking it further, if there are multiple applicable demographics then you can use a multiple regression model.
- Linear Regression for multi-valued data such as ratings
- Logistic Regression for unary data such as purchased or not (0/1)

#### Product Association
Related items can be used to make recommendations. If you are looking at this item you may want this other item.  Understanding the potential relationship between products can help inform potentially good recommendations. Factors such as price need to be kept in mind so that you don't recommend a product that is disproportional to the initial item.  Overall Product Association takes into account the present context to provide more relevant, non-personalized recommendations.  Can use attributes (such as the following) singularly or in combination:
- current item
- path
- time
- location

_Example: If you are looking at an electronic device, consider getting batteries and not the other way around._

_Simple approach:_ percentage of users who buy $X$ also bought $Y$ and divide by the count of $X$.  This doesn't account for any potential relevancy between $X$ and $Y$ and doesn't account for the general popularity of $Y$.
$$
X \wedge Y \over X
$$

Using another approach that's more controlled which doesn't let the popularity of $Y$ overwhelm $X$.  We can use the basis of Bayes' Law to do this.  This is defined as the probability that $B$ occurs given $A$ multiplied by the probability of $A$ to begin with and divide by the overall probability of $B$.
$$
\text {Bayes' Law} = P(A|B) = {P(B|A) P(A) \over P(B)}
$$

We can use this methodology to see how much more likely product $Y$ is to be purchased than it was before $X$ was purchased as the triggering event. The probability of $Y$ given $X$ divided by $X$.  So if the ratio comes close to 1, then $X$ didn't change anything. Basic formula:
$$
s(i|j) = P(i|j) = {P(i \wedge j) \over P(j)} = {\text{# of users rating both} / \text{# of users} \over \text{# users rating}\,\, j / \text{# of users}}
$$

_With Lift applied:_  The probability of $i$ and $j$ over the probability of $i$ times the probability of $j$.

So the frequency with which they appear together over what that frequency would be if they were entirely independent. A score of 1 means that the items are statistically independent from each other and a high score means that they're more likely to be bought together than we would expect if there was no connection between these items.
$$
s(i|j) = {P(i|j) \over P(i)} = {P(i \wedge j) \over P(i) \, P(j)}
$$

Further we can use __Association Rule Mining__. This tells us not whether $X$ makes $Y$ more likely or $Y$ makes $X$ more likely, but looks at how they make each other more likely.  This is usually used to evaluate a collections of items and make a recommendation based on that.
$$
P(X \,\, \text{AND} \,\, Y) \over P(X) * P(Y)
$$

### Content-Based Filtering
Establishing stable user preferences is key to making this approach work.  The key concepts are:
- model items according to relevant attributes
- model user preferences by attribute
- match those profiles together to make a recommendation

Users don't necessarily like to explicitly tell you what they are interested in, but do better at editing preferences that were learned.  These preferences can be learned through user actions and/or explicit user ratings.  Ultimately we want to merge implicit and explicit feedback into the user model.

Overall we need content attributes that can align with user preferences, they need to be compatible for comparison.  There also has to be a reasonable distribution of these attributes across items.  This approach also works well with a small set of users.

Use user rating or score for an item to show preference for the various item attributes.  The user model is a list of attributes (not items) and the specific user score for each.  When an item is rated (or other action that can be scored) that value is used to update the user model for all the item attributes.

Apply user model to new items to get a similarity score for item against user profile vector, essentially make a prediction if the user will like the item or not.

__Benefits:__  
- entirely content based
- able to make a recommendation with only a single user
- understandable / explainable profile
- profile is editable
- efficient computation
- ability to integrate with

__Drawbacks__
- simplified model
- can't handle interdependencies

### TFIDF
Term Frequency Inverse Document Frequency

_Term Frequency_ = Number of occurrences of a term in the document (e.g. simple count)
_Inverse Document Frequency_ = How few documents contain this term or how rare it is for a document to contain this term.

TFIDF can be used to create a profile of an item, a weighted vector of its tags or labels that explains what the item is about. This weighted vector can be folded in to the user profile when we identify that the user likes or dislikes this particular item.  This profile can then be matches against future items to make recommendations.

The origin of the tags/labels is important to understand.  If they are curated then it can be a trusted label, but if community driven, the label may need to appear more than once to trust it as an accurate descriptor of the item.  TFIDF is a good technique for mining relevant item labels.

_notation_:  
$t$
: term  
$d$
: document  
$D$
: all documents  
$f$
: frequency

TF-IDF for a single item term (or tag/label)
$$
q_{it} = f_{t,d} \cdot 1/\log (d \in D : t \in d)
$$

The tag vector for an item $i$
$$
\hat{\mathbf{q}_i} = \{q_{it_1},q_{it_2}, ..., q_{it_n}\}
$$

Normalize the tag vector to a unit vector by dividing each value by the Euclidean length of the whole vector
$$
\hat{\mathbf{q}_i} = {\overrightarrow{q} \over {\|\overrightarrow{q}\|}_2}
$$

### Vector Space User Profiles
As we build item and user profiles based on attributes we create a vector space model which represents user preference as a single scalar value for each dimension.  The potential draw back is that there is no difference between liking and importance.

Ways to build up profiles:
- add item vectors together?
  - do they need to be normalized or weighted?
- how do we use ratings or scores for items?
  - aggregate without weights
  - add positives and substract negatives
  - only add item above a threshold (e.g. 3.5 stars or more)
  - weighted, positive only (and possible negative too)
- updating profiles with new data
  - recompute each time (not efficient, wastes compute resources)
  - keep track of total weight and mix in new vectors (linear combination)
  - decay current profile and mix in new item vector

__threshold profile:__  
sum of the item-tag vectors of all items the user has rated positively (>= 3.5 stars)
$$
p_{ut} = \sum_{i \in I_u: r_{ui} \ge 3.5} q_{it}
$$

__weighted profile:__  
weighted sum of the item vectors for all rated items, with weights being based on the user's rating.  Subtract the users mean rating from each rating and multiply by the item tag value, then sum all these up.
$$
p_{ut} = \sum_{i \in I(u)} (r_{ui} - \mu_u) q_{it}
$$

### Predictions
- Cosine of the angle between the two vectors (profile, item)
  - cosine starts at its maximum, positive one, when the two vectors have the same angle and the angle between them is zero
  - grows negatively as that angle grows
  - then cycles back as they get closer together again
  - good measure that smoothly scales from saying this is a perfect match to this is exactly opposite of what the user likes. Then as it weaves its way back around through space, back through a perfect match again
  - this provides a score between -1 and 1 which can be used to make predictions. closer to 1 is better

__cosine similarity:__  
the score for an item is the cosine between that item's tag vector and the user's profile vector
$$
\mathrm{cos}(\mathbf{p_u},\mathbf{q_i}) = \frac{\mathbf{p_u} \cdot \mathbf{q_i}} {{\|\mathbf{p_u}\|}_2 {\|\mathbf{q_i}\|}_2} = \frac{\sum_t q_{ut} p_{it}}{\sqrt{\sum_t q_{ut}^2} \sqrt{\sum_t p_{it}^2}}
$$

[//]: # (this sis great way to do a comment)
