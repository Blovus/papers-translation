原文链接 http://citeseer.ist.psu.edu/viewdoc/download;jsessionid=2348B4F2359DA716B27BD40152F7939B?doi=10.1.1.27.6456&rep=rep1&type=pdf

https://link.springer.com/book/10.1007/BFb0022134



#                                                    再议无阻塞原子提交与共识的关系[^w1]

<center>Rachid Guerraoui</center>
<center>计算机科学系

<center>1015瑞士洛桑

<center>电子邮件：guerraoui@di.ep.ch<center>





### 摘要

本文论述了在具有*不可靠*失效检测器的异步系统[^**t1**][asynchronous systems with *unreliable* failure detectors]中，***无阻塞原子提交***[Non-Blocking Atomic Commitment]难题（**NB-AC**）和**共识**[Consensus]难题之间的关系。我们首先可以确认的是 **NB-AC** 要比**共识**棘手的多。与**共识**相反，**NB-AC**无法通过不可靠失效探测器处理，哪怕该难题只是一次单机的崩溃失效[^t2]。我们定义了一个相比**NB-AC**更易于处理的的难题，称之为***无阻塞弱原子性提交***[Non-Blocking Weak Atomic Commitment ]（**NB-WAC**），它可以解决大多数实际场景。**NB-WAC**的一个基本特征为其有对**共识**难题的*可约性*[reducibility] [^**t3**]。 因此，先前使用不可靠失效检测器解决**共识**的结果适用于**NB-WAC**。 这种可约性带来的一个有趣的中间结果是，**统一共识**[Uniform Consensus]和**共识**成了等价的难题。 事实结果表明，任何通过使用不可靠失效检测器解决**共识**的算法也可以解决**统一共识**。





## 1   介绍

在分布式系统中，为了确保事务失败时的原子性，必须解决参与进程之间的协定问题[^t4]。该问题被称之为**原子提交**难题（**AC**），要求参与者就事务结果达成共识——要么*提交*[commit]要么*中止*[abort]。当场景要求尽管部分参与者失效了，其余每个正常的参与者最终仍达成结果，该问题被称之为***无阻塞原子提交***难题（NB-AC）。 解决此问题可使正确的参与者放弃资源（例如锁），而无需等待崩溃的参与者恢复。 例如，***2段提交***[Two Phase Commit]（2PC）算法可以解决**AC**但无法解决**NB-AC** [2]，而***3段提交算法***[15]可以解决同步系统中的**NB-AC**难题（当通信发生延迟和进程的相对速度受限时）。在本文中，我们将比较异步系统在崩溃失效和可靠信道场景下的**NB-AC**难题和**共识**难题，并探讨了（可能是不可靠的）失效检测器[4]。



**共识**难题和**NB-AC**难题有一定的相似性，因为它们都是非阻塞协定问题。 二者皆适用于所谓的***FLP****不可能结果*[FLP impossibility result] [^t5]——在异步系统中，即使是只有一个进程崩溃失效，任何非平凡性[^t6]协定[7]也无法达成。 本文的创作出发点来自Chandra和Toueg [4]的基础结论，该结论指出**共识**在具有不可靠失效检测器的异步系统中是可以解决的。 这因此引出了一个有趣的问题——**NB-AC**是否也可以在具有不可靠失效检测器的异步系统中解决。



这个问题的答案是“否”，这不足为奇，因为人们认为**NB-AC**难题比**共识**更难[6，12]。 但是，与最初的直觉相反的是，**NB-AC**比**共识**更难的原因并不是其***一致同意***[Uniform agreement]条件[^w2]。 我们前面已经说过，基于不可靠失效检测器而言，**统一共识**（**共识** + **一致同意**）和**共识**是等价的问题。 解决**NB-AC**的难点实际上是由于***非平凡[Non-Triviality]***条件（*提交[commit]*必须确定所有参与者是否都投*赞成[yes]*票，并且*不存在失效[there is no failure]*）。 这个条件通常只是为了避免问题的平凡解[trivial soluarion]，这需要对失效的原因有准确的了解，而这是不可靠失效检测器无法提供的。



但是，在一个相对较弱的非平凡条件较的情况下（提交[commit]必须确定所有参与者都投票*赞成[yes]*且*察觉所有的未参与者[no participant is ever suspected]*），我们定义的问题要比**NB-AC**容易，即***NB-WAC（无阻塞弱原子提交）***。 这个难题在现实世界的事务系统中足以被处理。 **NB-WAC**的基本特征是，在具有不可靠失效检测器的异步系统中，它可以简化为**共识**难题，即只要**共识**可以解决，**NB-WAC**也可以解决。 Chandra和Toueg用不可靠失效检测器解决**共识**难题的结论[4]因此也适用于**NB-WAC**：（1）当存在至少一个参与者正确，则NB-WAC可以使用S级失效检测器处理；以及（2）如果有很多正确的参与者，则**NB-WAC**可以使用**◇S**级失效检测器处理[t7]。



本文的其余部分安排如下。 在第2节中，我们会描述我们的系统模型。 第3节中，我们定义了**NB-AC**，并证明它比**共识**更难处理。 在第4节中，我们将讨论基于不可靠失效检测器，**共识**和**统一共识**会成为等效的难题。 在第5节中，我们定义了**NB-WAC**，并表明它可简化为**共识**。 最后，第6节总结了本文的主要贡献，并讨论目前有关及未来的工作场景。



## 2    模型


带有异常检测的异步计算模型就是我们所描述的模型[4]。 下面，我们仅回顾本文中所需要的一些非正式的定义和结论。

### 2.1 进程

我们考虑的分布式系统由有限的一组进程Ω= {p₁,p₂......pₙ}通过一组信道完全连接。通信是通过消息传递实现的，*异步[asynchronous]*且*可靠[reliable]*。 进程仅会因为崩溃才失效； 不考虑拜占庭失效的情况。 异步意味着通信延迟或进程的相对速度不受限制。 可靠的信道可确保如果pᵢ和pⱼ正确（即不发生崩溃），则进程pᵢ发送到进程pⱼ的消息最终会被pⱼ接收。 为了简化模型表述，我们基于方便可以假设存在离散的全局时钟。这仅是进程无法访问的虚拟设备。 时钟刻度的范围是自然数的集合。历史进程pᵢ∈Ω 是事件hᵢ∈e<sub>i</sub><sup>0</sup> ·e<sub>i</sub><sup>1</sup>···e<sub>i</sub><sup>k</sup>的序列号，e<sub>i</sub><sup>k</sup>表示pᵢ进程在k时的事件。正确进程的历史是有限的。如果不是有限，进程pᵢ将终止于事件crash<sub>i</sub><sup>k</sup>（进程pᵢ于k时崩溃）。进程随时可能失败，我们使用ƒ表示可能崩溃的进程数。我们认为系统至少有一个进程正确（ 即ƒ<|Ω|）。



失效检测器是一个分布式的预言——它会提供有关失败进程的提示。我们考虑的算法会使用失效检测器。该算法定义了一连串*运行（runs）*，并且一次使用失效检测器D的算法A的运行作为一个元组 *R=<F,H<sub>D</sub>, I,S,T>*：*I*是*A*的初始化配置；*S*是*A*（由进程历史记录构成）的事件的无穷序号；*T*是一个随时间递增的值，表明了*S*中的每个事件何时发生； *F*是一个失效模式，表示在任何时间点t崩溃的进程集合*F(t)*；*H*是失效检测器的历史记录，它在每个时间点*t*的每个进程*p*都会产生，失效模式（可能为错误）的试图*H(p,t)*：*H(p,t)*表示一连串进程的场景中，q∈H(p,t)意味在时间点*t*时进程*p*怀疑进程*q*。

### 2.2 失效检测器级别

失效检测器的抽象话特征为*完整性[completeness]*和*准确性[accuracy]*这两个属性[4]。完整性是崩溃进程被正确进程永久怀疑的程度。 准确性限制了进程可能产生的错误怀疑。 完整性又被定义分为两种属性：***强完整性[Strong Completeness]***，即一段时间后，每个崩溃的进程都会被每个正确的进程永久怀疑；而***弱完整性[Weak Completeness, ]***，即一段时间后，每个崩溃的进程都会被某些正确的进程永久怀疑。准确性被定义为四种属性：***强准确性[Strong Accuracy]***，任何进程在崩溃前都不会被怀疑；***弱准确性[Weak Accuracy]***，即一些正确的进程不会被怀疑；***最终强准确性[Eventual Strong Accuracy]***，即在一段时间之后，正确的进程不会被其他正确的进程怀疑； 以及***最终弱准确性[Eventual Weak Accuracy]***，即在一段时间之后，一些正确的进程不会被其他正确的进程怀疑。



<table> 	
  <tr align="center"><th rowspan="2">完整性</th><th colspan="4" align="center">准确性</th></tr>
  <tr align= "center"> <td>Strong</td> <td>Weak</td><td>◇Strong</td><td>◇Weak</td></tr> 
  <tr align= "center"> <td>Strong</td> <td>P</td><td>S</td><td>◇P</td><td>◇S</td></tr>
  <tr align= "center"><td>Weak</td>   <td>Q</td><td>W</td><td>◇Q</td><td>◇W</td></tr> 	 
</table>

​                                                                                   图.1. 失效检测器级别

失效检测器级别意味着一组失效检测器具有相同的完整性和相同的准确性属性（图1）。例如，失效检测器***P***级别是一组具有***强完整性***和***强准确性***的失效检测器。 具有**强准确性**特征的失效检测器具备*可靠性[reliable]*：不会产生任何错误的怀疑。 其余的则为*不可靠*。 例如，失效检测器***S***是*不可靠*的，而失效检测器***P***是*可靠*的。



### 2.3 可约性与转换

如果每次运行算法 *A* 都满足问题 *B* 的要求，则表示算法 *A* 解决问题 *B* 。如果存在一种可以解决问题 *B* 的算法是基于使用任何为 *C* 级别的失效检测器，则表示问题 *B* 可以基于 *C* 级别解决。 如果基于 *C* 级别来解决问题 *B<sup>2</sup>* 的算法可以转换为基于 *C* 解决 *B<sup>1</sup>* ，则可以说是*B<sup>1</sup>*基于 *C* 级别可简化为 B<sup>2</sup> 。如果 *B<sup>1</sup>* 不能简化为为 *B<sup>2</sup>* ，我们说 *B<sup>1</sup>* *难于[harder than]* *B<sup>2</sup>*。

如果有那么一种基于失效检测器 C<sup>1 </sup>级别的算法，可以达到失效检测器 C<sup>2</sup> 的效果，则表示 C<sup>1</sup> *强于[stronger than]* C<sup>2</sup>（写作  C<sup>1 </sup>⪰C<sup>2 </sup>）。因此 如果 C<sup>1 </sup>强于 C<sup>2</sup>并且问题*B*基于 C<sup>2 </sup>解决，那么  C<sup>1 </sup> 也可以 解决 *B*。则明显可以推导出下列关系：*P*⪰*Q*，*P*⪰*S* ，*◇P*⪰*◇Q*，*◇P*⪰*◇S*，*◇P*⪰*◇S*，*S*⪰*W*，*◇S*⪰*◇W*，*Q*⪰*W*以及*◇Q*⪰◇*W*。正如事实所展示的那趟的那样，任意*弱完整性*的失效检测器都可以被转化成使用*强完整性*[^4],所以我们也能推导出如下关系 *Q*⪰*P* ，*◇Q*⪰*◇P*，*W*⪰*S* ，*◇W*⪰*◇S*。*S* 级别和 *◇P*无法比较。



### 2.4 共识

在**共识**难题（或简称为**共识**）中，每个参与者都*提出[proposes]*一个输入值，而正确的参与者们必须最终*决定[decide]*某个共同的输出值。 **共识**由以下条件指定。 ***同意[Agreement]***：不存在两个正确的参与者决定出不同的观点； ***统一有效[Uniform-Validity]***：如果一个参与者决定为*v*，则*v*必须由某个参与者提出；***终止[Termination]***：每个正确的参与者最终都会决定。 Chandra和Toueg指出了以下两个基本结果[^4]:

1.如果ƒ<|Ω|，**共识**基于 ***S* ** 解决。

2.如果ƒ<[|Ω|/2]，**共识**基于 ***◇S* ** 解决。



## 3    NB-AC难于共识

在本节中，我们会展示在具有不可靠失效检测器的异步系统中，**无阻塞原子提交**难题（或简称为**NB-AC**）无法解决。 即使假设最多只有一个进程可能发剩崩溃，该结论的可能性依然成立。 因此，**NB-AC**比**共识**更难。



### 3.1 非阻塞原子提交难题

原子提交难题是分布式事务系统的核心。 事务源自称为**事务管理器[Transaction Manager]（缩写为TM）**的进程，该进程通过与各种称为**数据管理器[Data Managers]**（缩写为DM）的进程进行交互来访问数据。 **TM**首先执*开启事务[begin-transaction]*操作，然后执行各种*写[write]*和*读[read]*操作（通过将写和读转换为消息发送到DM），最后执行*结束事务[ end-transaction]*操作。 为了确保事务所谓的*失效的原子性（ failure atomicity）*，所有已执行写操作的DM必须解决***原子提交[Atomic Commitment]***难题（作为*结束事务*操作的一部分）。 这些DM称为难题的参与者。 在本文中，我们假设参与者彼此知道对方的存在，并且了解事务的含义[1]。



原子提交难题要求参与者们在事务中从两个可能的值（*提交[commit]*和*中止[abort]*）之间*达成[reach]*共识。这种情景我们称之为这个参与者*AC-决定提交[AC-decide commit]*（另一种选择则称为*AC-决定中止[AC-decides abort]*）。当且仅当参与者们AC-决定[^t7]*提交*时，DM们所执行的写操作才变成永久的。单个参与者AC-决定的结果取决于参与者们提供的*投票[votes]*（*赞成[yes]*或*反对[no]*）。我们将称之为一位参与者*投赞成票[vote yes]*（另一场景则称之为*投反对票[vote no]*）。每次投票都反映了参与者有能力确保其数据的更新可以变成永久的。我们对投票的定义不做任何假设，除非投票预先无法确定。例如，当且仅当在本地未检测到并发控制冲突并且更新已被写入稳定存储时，这个参与者才投*赞成*票。否则，参与者投*反对*票。仅当所有参与者都投*赞成*票时，单个参与者才能AC-决定*提交*。为了排除参与者们总是AC-决定*中止*的琐碎情况，通常要求如果所有投票均为*赞成[yes]*，并且没有参与者崩溃，则必须决定提交[^2]。



我们要考虑*无阻塞原子提交难题（NB-AC）*在以下的情况——即使某些参与者崩溃了，也要由某个正确的参与者来AC-决定。**NB-AC**基于以下条件：

——***一致同意[Uniform-Agreement]***:两个参与者不能AC-决定出不同的结果。

——***一致有效[Uniform-Validity]***:如果某个参与者AC-决定*提交*，那么接下来其他参与者应该投*赞成*票。

——***终止[Termination]***：每个正确的参与者最终都要AC-决定。

——***非平凡[NonTriviality]***：如果所有参与者投*赞成*票，并且不存在失效的情况下，那么每一个正确的参与者最终都会AC-决定*提交*。

*一致同意*和*一致有效*是安全性[safety]条件。 它们确保事务的*失效的原子性*这个属性。 *终止*是保证活性[liveness]的条件——活性为非阻塞提供了保证。 *非平凡*排除了难题的平凡解（参与者总是AC-决定*中止*）。*非平凡*从应用程序的角度来看也可以视为活性条件，因为它可以确保进程（即事务提交）处于合理的预期下——没有崩溃且没有参与者投票*反对*。



### 3.2 NB-AC难题无法解决

我们已经说明了NB-AC难题难于共识难题——哪怕只有单个就节点的崩溃，不可靠失效检测器也无处理解决[not strong enough to solve]NB-AC。我们对这种结果表示为 ***S***和***◇P***级别（章节2.3）。因此我们的结果只支持***◇S***，***◇Q***，***W***，***◇W***。



**定理1**：*如果ƒ>0,不论**◇P**还是 **S**级都无法解决NB-AC。*

**证明**：（反证法[^W3]）。我们设想存在可以解决NB-AC难题的算法***A***，并且该算法使用任意的失效检测器***◇P***（另一种场景使用***S***）。设想存在一个为***◇P***（另一种场景使用***S***）级别的失效检测器***D*** 并且存在基于算法***A***的运行***R=<F,H<sub>D</sub>, I,S,T>***。在R中，所有的参与者都投*赞成*票。一个参与者p<sub>1</sub>快速崩溃，没有发送消息通知其他参与者，而其他参与折此时都为正确。设想存在一个正确的参与者p<sub>2</sub>。如果p<sub>2</sub>没有AC-决定,那么运行R就违反了NB-AC的*终止*条件——因此结论矛盾。因此假设在时间点***t***，p<sub>2</sub>做出了AC-决定——提交（情况1）或中止（情况2）。基于这两种情况我们分别进行推到：

​        情况1.*p<sub>2</sub>于t时AC-决定提交*。设想基于算法*A*的运行R<sup>1</sup>=<F,H<sub>D</sub>, I<sup>1</sup>,S,T>,R<sup>1</sup>和R几乎完全相同，除了在R<sup>1</sup>里p<sub>1</sub>为投*反对*票（而不是R中的*赞成*）。参与者p<sub>2</sub>在R<sup>1</sup>执行的场景和R一样，并且在t时AC-决定*提交*（p<sub>2</sub>在R<sup>1</sup>和R中的表现没有任何区别）。因为存在一个参与者（即p<sub>1</sub>）投票*反对*，那么基于算法A的运行R<sup>1</sup>就违反了NB-AC的*一致有效*条件——因此产生悖论。

​        情况2.*p<sub>2</sub>于t时AC-决定终止。*设想基于算法*A*的运行R<sup>2</sup>=<F<sup>2</sup>,H<sup>2</sup><sub>D</sub>, I<sup>2</sup>,S<sup>2</sup>,T<sup>2</sup>>。在R<sup>2</sup>中，所有的参与者（包括p<sub>1</sub>）都为正确，并且p<sub>1</sub>的所有消息都延迟到 t'>t 时才会被接受。假设H<sup>2</sup><sub>D</sub>和H<sub>D</sub>完全相同，除了在 t'>t 时之后，p<sub>1</sub>永远不会被H<sup>2</sup><sub>D</sub>怀疑。为了满足H<sup>2</sup><sub>D</sub>的***强完整性***，在R<sub>3</sub>[^t8]中，应该没有参与者崩溃。那么我们接下来继续考虑***准确性***相关的内容。

​                --如果D的级别为***◇P***，H<sub>D</sub>则必须满足***最终强准确性***，即存在某一时间点，在这之后正确的参与者们不会被任何其他正确的参与者怀疑。假设H<sup>2</sup><sub>D</sub>和H<sub>D</sub>完全相同，除了在 t'时之后p<sub>1</sub>不会被任何参与者怀疑，那么H<sup>2</sup><sub>D</sub>则满足了***最终强准确性***。

​                --如果D的级别为***S***，H<sub>D</sub>则必须满足***弱强准确性***，即存在某个正确的参与者p<sub>k</sub>从不被H<sub>D</sub>怀疑，另外p<sub>k</sub>≠p<sub>1</sub>（因为此时p<sub>1</sub>在R中已经发生崩溃），那么者p<sub>k</sub>从不被H<sup>2</sup><sub>D</sub>怀疑。因此H<sup>2</sup><sub>D</sub>则满足了***弱强准确性***。

​        直到时间点t时，参与者p<sub>2</sub>在R执行的场景和R一样，并且在t时AC-决定*提交*（直到t时，p<sub>2</sub>R在<sup>2</sup>和R中的表现都没有任何区别）。这样一来，因为所有的参与者们都为正确并且都投票*赞成*，那么基于算法A的运行R<sup>1</sup>就违反了NB-AC的*非平凡*条件——因此产生悖论。



通过失效检测器级别之间的关系（第2.3节），我们推导出以下结论。

**推论1.** *如果ƒ>0,不论**◇Q**，**S**，**◇S**或者**W**级都无法解决NB-AC。*



根据直觉，无法用不可靠失效检测器解决**NB-AC**的原因是**NB-AC**需要有关失效的确切知识。 假设一个参与者*p*既不知道所有参与者都投了投票*赞成*，也不知道某个参与者投票*反对*。 参与者*p*不能无限期地等待所有参与者的投票（有些参与者可能已经崩溃），并且*p*也不能AC决定*中止*，除非它知道某个参与者已经崩溃了。 一个不可靠失效检测器（可能会引起错误的失效怀疑）不会给p这样的知识，因此不够无法胜任来解决**NB-AC**。 NB-AC的***非平凡***条件中包含对失效的精确知识的需求。 令人惊讶的是，这个旨在消除平凡解的条件在该问题中引出了一个了棘手的困难。 在第5节中，我们会弱化***非平凡**条件，但仍排除了平凡解，因此一个新的相对易处理问题可以通过不可靠失效检测器处理解决。



## 4    **统一共识** 等价于 **共识**

通常来说，**原子提交[Atomic Commitment]**难题被认为比**共识[Consensus]**难题更难处理，这是由于其***一致同意***的条件所致（而不是由于其非平凡条件）[6，12]。 广义上讲，**共识**允许使两名参与者做出不同的决定——只要其中至少有一名参与者崩溃，然而**原子提交**难题不允许两名参与者做出不同的AC决定（无论这些参与者是否崩溃）。



在下面的内容中，我们将说明在具有不可靠失效检测器的异步系统中，***统一共识***（**共识**+*一致同意*）可简化为**共识**，即只要**共识**能够解决，**统一共识**也能解决。



### 4.1 非阻塞原子提交难题

**统一共识** 难题的决定性条件有**共识**的***统一有效[Uniform-Validity]***和***终止[Termination]***条件（第2.4节）以及下面的***一致同意***[Uniform-Agreement]条件：

​	——**一致同意**：不存在两个参与者（不管正确与否）做出不相同的值的决定。



首先，我们设定一个具有*强完整性*的不可靠失效检测器。这个失效检测器可以为 ***P***，***S***和***◇P***（图1）。当我们讨论完推论2后，我们再回过头来接着讨论*弱完整性*。



为了证明通过***◇P***（其他场景使用***S***，***◇S***）**统一共识**可简化为**共识**。为此需要证明——如果存在使用***◇P***的算法**A**可以解决**共识**，那么我们也可以创建一种使用***◇P***（其他场景使用***S***，***◇S***）的算法**A'**。下文的推论2甚至能证明算法**A'**就是算法**A**本身。



**定理2.**  *任何通过 **◇P**（其他场景使用**S**，**◇S**）能解决**共识**的算法，也可以通过**◇P**（其他场景使用**S**，**◇S**）解决**统一共识***。

**证明**：设想存在某个使用***◇P***（其他场景使用***S***，***◇S***）的失效检测器***D***。如果存在使用***D***的算法***A***处于运行***R***中无法解决**统一共识**，**A**在R<sup>1</sup>也无法解决**共识**。



设想存在***A***运行于运行***R=<F,H<sub>D</sub>, I,S,T>***里，因此***R***不满足***一致同意***条件但满足**共识**的条件（否则，***R<sup>1</sup>***明显就是***R***本身了）。在运行***R***中，两位参与者**p<sub>i</sub>**和**p<sub>j</sub>**决定出的数值不同并且其中至少有一个发生崩溃，这里暂且假设崩溃的是**p<sub>i</sub>**。假设**p<sub>i</sub>**于**t<sub>i</sub>**时决定的值为**v<sub>i</sub>**，而**p<sub>j</sub>**于**t<sub>j</sub>**时决定的值为**v<sub>j</sub>**（v<sub>i</sub>≠v<sub>j</sub>）。



假设存在运行***R<sup>1</sup>=<F<sup>1</sup>,H<sup>1</sup><sub>D</sub>, I<sup>1</sup>,S<sup>1</sup>,T<sup>1</sup>>***和***R***有相同的失效情况，除了**p<sub>i</sub>**和**p<sub>j</sub>**在**R<sup>1</sup>**中都为正确。在***R<sup>1</sup>***中，**p<sub>i</sub>**和**p<sub>j</sub>**发送的消息接受都产生了延迟，R在*max(t<sub>i</sub>,t<sub>j</sub> )*之前都没有收到消息，直到 **t'>max(t<sub>i</sub>,t<sub>j</sub> )**时才收到。假设**H<sup>1</sup><sub>D</sub>**和**H<sub>D</sub>**直到 **t'**时刻前完全相同，并在 **t'**之后正确的参与者们没有一个会被怀疑并且每个崩溃的参与者都被永久的怀疑。因此很明显H<sup>1</sup><sub>D</sub>满足***强完整性***，***最终强准确性***以及***最终弱准确性***。又因为除了这些怀疑外，H<sup>1</sup><sub>D</sub>不存在其他任何怀疑，所有如果**H<sub>D</sub>**满足**弱准确性**，,H<sup>1</sup><sub>D</sub>也满足**弱准确性**。



值得注意的是，[4]中描述的算法最初是为了解决通过***S***和***◇S***解决**共识**而设计的，但该算法实际也解决了**统一共识**。



通过失效检测器级别之间的关系（第2.3节），我们得出下面的推论2。

**推论2.**  ***统一共识****可基于**◇P**（或使用**◇Q**，**S**，**W**，**◇S**，**◇W**）简化为**共识**。*



下面的推论3基于先前处理**共识**的结论（第2.4节）及各个级别的失效检测器之间的关系。

**推论3.** 如果*ƒ<|Ω|*,统一共识可以被**S**或**W**解决，如果*ƒ<[|Ω|/2]*,**统一共识**可以被**◇S**，**◇P**，**◇Q**，**W**或**◇W**解决。



### 4.2统一共识 与共识在可靠失效检测器下的比较

本章节我们会论述定理2无法支持***P***（因此也无法支持***Q***）。[^w4]我们提出一个算法*A*——其使用任意***P***级别的失效检测器解决共识问题，但是存在某一P级别的检测器D，基于算法A的D无法解决共识问题。该算法可以可以被描述为图2的函数consensus(v<sub>i</sub>)，每一个参与者称之为P<sub>i</sub>，v<sub>i</sub>表示p<sub>i</sub>所支持的输入值。函数consensus()终止于“return outcome”语句，outcome即为最终决定的值（第8行）：当p<sub>i</sub>执行return outcome时，p<sub>i</sub>就要决定结果。参与者p<sub>i</sub>会被本地的失效检测器模块D<sub>i</sub>告知失效的怀疑信息：符号p<sub>j</sub>∈D<sub>i</sub>(第3行)表明p<sub>i</sub>怀疑p<sub>j</sub>。



![可以解决共识但无法解决统一共识的算法A](pic/pic1.png '''')

​																图2.可以解决共识但无法解决统一共识的算法A



算法的基础设想如下。参与者p<sub>1</sub>立刻发送决定消息*（p<sub>1</sub>,v<sub>1</sub>,decide）*（第7行）（绕过2-6行），并决定提交*v<sub>1</sub>*（第8行）。如果p<sub>2</sub>在收到（p<sub>1</sub>,v<sub>1</sub>,decide）*前没有怀疑p<sub>1</sub>，那么p<sub>2</sub>将支持p1（第5行）。接下来p2将*（p<sub>2</sub>,v<sub>2</sub>,decide）*（v1=v2）发送给所有人（第7行），并决定提交*v<sub>1</sub>*（和p1做出一样的决定）*（第8行）。参与者p3将一直等待直到它收到*（p<sub>1</sub>,v<sub>1</sub>,decide）*或它怀疑p<sub>1</sub>，以及*（p<sub>2</sub>,v<sub>2</sub>,decide）*或怀疑p<sub>2</sub>。当出现p<sub>3</sub>收到二者的消息且v<sub>1</sub>≠v<sub>2</sub>的情况下，p<sub>3</sub>将支持v<sub>2</sub>。概括来说，参与者决定的输入值由p<sub>k</sub>提出，k代表着参与者中不被怀疑的最小指数。



**定理3.1.** *图2的算法基于p级别可以解决共识*。

**证明**：我们将证明共识难题需要的三个条件。

1.*同意（Agreement）*。假设有一个正确的参与者p<sub>i</sub>决定了值为v。由于p<sub>i</sub>正确，那么基于*D*的强准确性，不会有参与者怀疑p<sub>i</sub>。p<sub>i</sub>必须在决定前（第8行）发送它的决定消息*（p<sub>i</sub>,v,decide）*（第7行），接下来假定所有消息渠道都非常可靠，每一个正确的参与者都收到了*（p<sub>1</sub>,v,decide）*。因此每个参与者p<sub>k</sub>（其中i<k）都会在决定前收到*（p<sub>1</sub>,v,decide）*（第4行）。因此p<sub>k</sub>决定数值为v（第5行）。

2.*有效（Validity）*。每一个结果都有一些参与者决定,这是由于每一个输入值都由一些参与者提出。

3.*终止（Termination）*。基于*i*的感应可知——每一个正确的参与者p<sub>i</sub>最终都会做出决定。如果p<sub>1</sub>为正确则发送一个决定消息（第7行）并做出决定（第8行）。假设每一个*k*>1，如果pk正确则最终做出决定，并考虑p<sub>k+1</sub>。由于做出决定（第8行）前每一个正确的参与者都必须在发送决定消息（第7行），接下来假设基于***D***的强一致性条件和可靠的渠道，p<sub>k+1</sub>在第2行的状态无法保持未定义的锁定。每一个j<(k+1),要么p<sub>k+1</sub>收到决定的消息*（p<sub>j</sub>，v<sub>j</sub>，decide）*，要么pk+1怀疑pj。因此，如果p<sub>k+1</sub>是正确的，那么p<sub>k+1</sub>最终会做出决定。





**定理3.2.** *图2的算法基于p级别无法解决统一共识*。

**证明**：定义存在基于***P***的失效检测器***D***，并运行在违反统一共识条件的算法下。假设运行环境***R=<F,H<sub>D</sub>, I,S,T>***，p<sub>1</sub>和p<sub>2</sub>有不同的输入值。假设在**H<sub>D</sub>**中的每一个进程，崩溃的进程被永久怀疑，正确的进程则从不被怀疑。因此**H<sub>D</sub>**保证了强完整性和强准确性。假设p1在决定出数值v<sub>1</sub>（第8行）后迅速崩溃，而他的决定消息*（p<sub>1</sub>，v<sub>1</sub>，decide）*（第7行）无法抵达p<sub>2</sub>.因此p<sub>2</sub>怀疑p<sub>1</sub>并决定出数值v<sub>2</sub>（v<sub>1</sub>≠v<sub>2</sub>）。这样一来，在运行环境R中，p1和p2做出了不同的决定。





通过***P***级别和***Q***级别失效检测器的关系，定理3.1和定理3.2也适用于***Q***。显而易见的是，图2的算法无法解决基于失效检测器的共识问题。甚至于如果p<sub>1</sub>和p<sub>2</sub>皆为正确，但p<sub>2</sub>却错误地怀疑p<sub>1</sub>，双方可能会决定出不同的数值。



## 5    NB-WAC 可简化为共识问题

在本节中，我们将定义非阻塞弱原子提交问题（或着简称为NB-WAC），比起NB-AC少了非平凡性条件。接下来我们要展示在使用不可靠失效检测器的异步系统中，NB-WAC可以简化为统一共识问题。这意味着，基于定理2，NB-WAC也可以简化为共识问题。





### 5.1 非阻塞弱原子提交问题

NB-WAC的定义包含了NB-AC难题（章节3.1）一样的三个条件——*一致同意*，*一致有效*，*终止*，以及以下的*非平凡性*条件：

——***非平凡[NonTriviality]***：如果所有参与者投票*赞成*，并且没有参与者被怀疑，那么每一个正确的参与者最终都会AC-决定*提交*。



所有的我们认为的***弱完整性***（所有崩溃的参与者最终都会被怀疑）失效检测器级别，NB-WAC的非平凡性条件弱于NB-AC的非平凡性条件。然而，NB-WAC的非平凡性条件仍然排除了平凡性场景的难题——参与者总是AC-决定*终止*[^w5]。要注意的是，一个能够解决NB-AC的算法如果基于一个潜在的失效检测器（该检测器总是怀疑某些进程）可能总是会导致事务的终止。然而实际上，失效检测器不会发生这种事。失效检测器通常通过超时来实现并且直到过期超时才会被怀疑。在实际的系统中，过期超时（不管这个怀疑是否正确）通常被认为是中止事务的一个充分理由。





### 5.2 NB-WAC简化为统一共识

我们把简化的算法描述为图3第函数*atomicCommitment()*，可以将任何解决基于 **◇P**（其他场景使用**S**，**◇S**）的统一共识算法转化为 基于**◇P**（其他场景使用**S**，**◇S**）解决NB-WAC的算法。

我们假设每一个参与者互相知道彼此的存在，并且每一个参与者p，要么崩溃了，要么在调用*atomicCommitment()*。参与者pi的投票表示为voitei，我们将*uniformConsensus()*（调用5-7行）表示为统一共识算法。*uniformConsensus()*提供返回的数值会被*提交*或者*中止*。函数*atomicCommitment()*会在“return outcome”条件处结束，此时*结果*要么*提交*要么*终止*（第6-8行）：当pi执行到*返回*结果时，pi会AC决定结果。参与者pi将会被本地的实效检测器告知崩溃的怀疑信息：符号*p<sub>j</sub>∈D<sub>i</sub>*(第3行)*(D<sub>i</sub>∈{◇P<sub>i</sub>,S<sub>i</sub>,◇S<sub>i</sub>})*表明p<sub>i</sub>怀疑p<sub>j</sub>。

基础的算法设想如下。每一个参与者发送他的投票给所有参与者（包括自身）。当一个参与者收到一张*反对*票或怀疑其他参与者时，通过提出*中止*（第5行）来开始统一共识，并通过返回统一共识（第6行）来AC-决定结果。当每个参与者从所有参与者处收到*赞成*票，通过提出*提交*（第7行）来开始统一共识，并通过返回统一共识（第8行）来AC-决定结果。

![将NB-WAC简化为统一共识的算法](pic/pic2.png '''')

​																       图3.将NB-WAC简化为统一共识的算法



**定理4.** *图3的算法基于 **◇P**级别，**S**级别还是**◇S**级别都可将NB-WAC难题简化为统一共识算法难题*。

**证明**：我们接下来讨论NB-WAC被定义的四个条件。

1. *一致同意*。每个AC-决定*结果*（第6-8行）的参与者，必须通过*统一共识*（第5-7行）来做出决定。基于统一共识的*一致同意*条件，不存在两个参与者做出不同决定的情况。
2. *一致有效*。如果一个参与者想要AC-决定*结果*，就只能通过*统一共识*来决定结果。基于统一共识的*一致有效*条件，一个参与者只能在某个参与者*p*提出*提交*（第7行）时，才能决定*提交*。为了能够执行到第7行，*p*必须收到所有参与者的赞成票。
3. *终止*。关于参与者*p*我们有两种场景需要考虑：（3.1）*p*收到了所有人的*赞成*票，或者（3.2）*p*没有收到。在场景（3.1）中，*p*开始了统一共识（第7行）。在场景（3.2）中，如果*p*收到了任意一张*反对*票，*p*开始了统一共识（第5行）。否则，当每个正确的参与者发送了它的投票，那么假设拥有可靠的渠道和**◇P**（其他场景使用**S**，**◇S**）级别的*最终强一致性*性质，*p*最终会怀疑某个参与者并开始统一共识（第5行）。因此每一个正确的参与者都会开始统一共识。基于统一共识·的*终止*条件，每一个正确的参与者最终做出决定并且是AC-决定。
4. *非平凡性*。如果不存在怀疑并且所有人投票赞成，那么每个开始*统一共识*的参与者都会提议*提交*（第7行）。基于统一共识的*一致有效*的条件，每一个正确的参与者决定*提交*且是AC-决定提交（第8行）。



根绝推论3中实效检测器中各级别的关系（章节2.3），我们得到推论4。

**推论4.**  *如果ƒ<|Ω| ，**S**或**W**都可以解决NB-WAC；如果ƒ<[|Ω|/2]， **◇S**，**◇P**，**◇Q**或**◇W**都可以解决NB-WAC。*

NB-WAC算法可以通过梳理图3的规约算法和基于**S**和**◇S**解决共识的算法来获得[4]。比较得知，[1,15]介绍的算法例子可以视作使用*P*的算法。在其他地方，我们会介绍使用**◇S**的集中和非集中的***三段提交（Three  Phase Commit）***算法[9,10]。



## 结论

本文的重点是为了扩展Chandra和Toueg[4]所探讨的如何通过不可靠失效探测器在异步系统（崩溃失效和可靠渠道）中处理难题的相关成果的应用领域。在[4，11，13，14]中，我们已经讨论了这些难题不同于*共识*这一通用性结论。据我们所知，（非阻塞）原子提交问题还是第一次在基于不可靠探测器的异步系统中被讨论。

通过弱化了原子提交的*非平凡性*条件，我们定义了一种问题——非阻塞弱原子性提交（NB-WAC），该问题足以应付实际的事务系统。我们证明了（1）统一共识可以简化为共识，（2）NB-WAC可以简化为统一共识。所以我们可以推论得到，Chandra和Toueg使用不可靠失效探测器来处理共识的结论同样适用于NB-WAC。

依据失效探测器的的特征，我们想要定义NB-WAC容错能力的下限。我们已知**◇W**作为最弱的实效探测器级别可以解决共识，以及当大多数参与者都可能失效时**◇P**无法解决共识。有趣的是这两种下限不管哪个都和NB-WAC有关。此外，有人可能会问，本文所定义的非平凡性是否足够强力来通过不可靠失效检测器处理难题。最终，我们并没有考虑在已知有限数量错误怀疑下的失效探测器。NB-WAC可以简化为共识这一论点在基于这些失效探测器时不管二者中的哪种都还尚无定论。



## 感谢

我对André Schiper表示深深的感谢，他对我的帮助尤为重要。本文论述的内容经过Aleta Ricciardi和检查组的修改建议而大大提高。我再此还想感谢Tushar Chandra, Vassos Hadzilacos, Mikel Larrea和Sam Toueg，他们与我有过不少有趣的探讨。





## 参考文献

1. O. Babaoglu and S. Toueg. Non-Blocking Atomic Commitment. In Distributed Systems, pages 147-166. Sape Mullender ed, ACM Press, 1993.
2. P.A. Bernstein, V. Hadzilacos, and N. Goodman. Concurrency Control and Recovery in Database Systems. Addison Wesley, 1987.
3. T. Chandra, V. Hadzilacos and S. Toueg. The Weakest Failure Detector for Solving Consensus. Proceedings of the 11th ACM Symposium on Principles of Distributed Computing, pages 147-158. ACM Press, 1992.
4. T. Chandra and S. Toueg. Unreliable failure detectors for reliable distributed systems. Technical Report, Department of Computer Science, Cornell Univ, 1994. A preliminary version appeared in the Proceedings of the 10th ACM Symposium on
Principles of Distributed Computing, pages 325-340. ACM Press, 1991.
5. B. Coan and J. Welch. Transaction commit in a realistic timing model. Distributed Computing, pages 87-103. 4(2), 1990.
6. D. Dolev and R. Strong. A Simple Model For Agreement in Distributed Systems. In Fault-Tolerant Distributed Computing, pages 42-50. B. Simons and A. Spector ed, Springer Verlag (LNCS 448), 1987.
7. M. Fischer, N. Lynch, and M. Paterson. Impossibility of Distributed Consensus with One Faulty Process. Journal of the ACM, pages 374-382. (32) 1985.
8. J. Gray. A Comparison of the Byzantine Agreement Problem and the Transaction Commit Problem. In Fault-Tolerant Distributed Computing, pages 10-17. B. Simons and A. Spector ed, Springer Verlag (LNCS 448), 1987.
9. R. Guerraoui, M. Larrea and A. Schiper. Non-Blocking Atomic Commitment with an Unreliable Failure Detector. To appear in Proceedings of the 14th IEEE Symposium on Reliable Distributed Systems, 1995.
10. R. Guerraoui and A. Schiper. The Decentralized Non-Blocking Atomic Commit ment Protocol. To appear in Proceedings of the 7th IEEE Symposium on Paral lel and Distributed Processing, 1995.
11. R. Guerraoui and A. Schiper. Transaction model vs Virtual Synchrony model: bridging the gap. In Distributed Systems: From Theory to Practice, pages 121-132. K. Birman, F. Mattern and A. Schiper ed, Springer Verlag (LNCS 938), 1995.
12. V. Hadzilacos. On the relationship between the atomic commitment and consensus problems. In Fault-Tolerant Distributed Computing, pages 201-208. B. Simons and A. Spector ed, Springer Verlag (LNCS 448), 1987.
13. L. Sabel and K. Marzullo. Election Vs. Consensus in Asynchronous Systems. Technical Report TR95-1488, Cornell Univ, 1995.
14. A. Schiper and A. Sandoz. Primary Partition \Virtually-synchronous Communication" Harder than Consensus. Proceedings of the 8th International Workshop on Distributed Algorithms, pages 39-52. Springer Verlag (LNCS 857), 1994.
15. D. Skeen. NonBlocking Commit Protocols. In Proceedings of the ACM SIGMOD International Conference on Management of Data, pages 133-142. ACM Press, 1981.



## 作者注释

[^w1]: 出现在国际研讨会有关分布式算法的会议记录中，出版商(LNCS)，1995
[^w2]:***一致同意[Uniform agreement]***条件禁止任何两个参与者（无论正确与否）做出不同的决定。 **NB-AC**需要***一致同意[Uniform agreement]***，而**共识**仅需要达成***同意[agreement]***（两个正确的参与者不能做出不同的决定）。

[^w3]:这个证明的思路在[5]中给出。

[^w4]: 注意这并不意味着同意共识不能简化为共识。
[^w5]:Coan和Welch在[5]有讨论定义一个弱*平凡性*条件来发展随机化的**非阻塞原子提交**协议的益处。



## 译者注释

[^**t1**]:failure detectors是一种算法，有时国内也直接翻译为失效检测算法。该算法主要用来判断一个节点是否dead。最易理解的常见的故障检测器就是超时算法——超时之后就算节点已经故障。相关描述可参考论文《Unreliable Failure Detectors for Reliable Distributed Systems》。https://www.cs.utexas.edu/~lorenzo/corsi/cs380d/papers/p225-chandra.pdf
[^t2]:关于failure和fault：虽然很多场景二者含义相同，注意  故障（fault）不同于失效（failure）。故障通常定义为系统的一部分状态偏离其标准，而失效则是系统作为一个整体停止向用户提供服务。故障的概率不可能降到零，因此最好设计容错机制以防因故障而导致的失效。 崩溃失效原文为crash failure。之后出现的失效原文皆failure。

[^t3]:通俗的说就是可以通过**NB-WAC**将场景简化为**共识**问题。
[^t4]:原文为agreement problem，暂且这么翻译，与国内常见的protocol（协议）区分。下文中出现的 “协定”如无特别说明原文皆为agreement。这个词在文中的含义其实就是我们常说的达成共识协议。另，国内有的文章也会将其翻译为”一致性”或“共识”而事实上，而国外的一些早期文献确实是将consensus叫做agreement的。
[^t5]:.由Fischer、Lynch和Patterson三位科学家于1985年发表的论文《Impossibility of Distributed Consensus with One Faulty Process》指出：**在网络可靠，但允许节点失效（即便只有一个）的最小化异步模型系统中，不存在一个可以解决一致性问题的确定性共识算法**（No completely asynchronous consensus protocol can tolerate even a single unannounced process death）。

以上结论被称为**FLP不可能原理。**该定理被认为是分布式系统中重要的原理之一。

此定理实际上告诉人们，不要浪费时间去为异步分布式系统设计在任意场景下都能实现共识的算法。

[^t6]:Non-Triviality。非平凡实际是数学用语。同理，文中出现的平凡解也是数学用语。简单解释下，我们在解一些微分方程的时候，无需计算就能看到答案之一为零，存在零解，但我们更关心的是那些非零解。这种场景下，零解就是平凡解，非零解非平凡解释。实际生活中，当女神抱怨怎么没人追她的时候，高富帅就是女神期望的非平凡解，而身边舔狗对女神来说就是平凡解。 所以平凡解也可以理解为无意义的答案。在文中的具体场景含义后文括号内作者有解释。
[^t7]:AC-decide 为动词，意思应该就是 基于原子提交（AC）的决定。本文译作AC-决定。
[^t8]:这里的R<sub>3</sub>估计是作者笔误，联系上下文应为R<sup>2</sup>。

分布式系统，或者并发程序的正确性需要满足两条：

- Safety. Something (bad) will **not happen** during a system execution
- liveness. Eventually something (good) **must happen** during an execution

一般一个算法的正确性包括两个方面：safety和liveness

- Safety：描述算法哪些行为是允许发生的（达成的commit之后不能丢失）；
- Liveness：描述算法哪些行为是一定要发生的（比如最终会达成commit）；





拜占庭将军问题 source process（主公进程） ，faulty processes（反贼进程），correct processes（忠诚进程）



[^**t4**]: 