% Kex in 2021: Ups and Downs
% Azat Abdullin

# Kex

* white box fuzzer for JVM bytecode
* based on symbolic execution
* test generation for Java and Kotlin


################################################################################

# What happened in 2021

* participation in SBST Java tool competition 2021\footnote{Panichella S. et al. Sbst tool competition 2021 //2021 IEEE/ACM 14th International Workshop on Search-Based Software Testing (SBST). – IEEE, 2021. – С. 20-27.}
* improving Java standard library support
* evaluation of Reanimator


################################################################################

# SBST 2021

Automatic test case generation competition

* 6 test projects with 98 benchmarks
* 30 and 120 second time budgets
* 4 participaiting tools and 1 baseline


################################################################################

# SBST 2021 results

![](sbstCoverage)


################################################################################

# SBST 2021: Kex results\footnote{Abdullin A., Akhin M., Belyaev M. Kex at the 2021 SBST Tool Competition //2021 IEEE/ACM 14th International Workshop on Search-Based Software Testing (SBST). – IEEE, 2021. – С. 32-33.}

\small
* Kex was ranked fifth with score of 44.21
* Kex achieved any coverage only on one project
	* average coverage of \~20\%
* Kex failed on 5 out of 6 projects
	* 1 project failed because of unhandled ASM error
	* 2 projects failed because Kfg encoutered some unexpected bytecode 
	* 2 projects failed because Kex required too much RAM
* Kex (and Reanimator) failed on some of the more complex language features (abstract classes, inner classes, etc.)
* Kex required too much of disk space

***Main teakeaway: Kex had a low level of maturity***


################################################################################

# SBST 2021: implications

* Kfg and Kex were optimized w.r.t. RAM usage:
	* Kfg currently uses \~2 times less RAM
	* Kex uses only one copy of each classes of PUT
* Kex and Reanimator were extended to support some new language features

\begin{center}
\begin{tabular}{|r|c|c|}
\hline 
\textbf{}    & \textbf{30s} & \textbf{120s}\\ 
\hline
\texttt{line coverage} & 22.31\% & 26.70\%\\ 
\hline
\texttt{branch coverage} & 14.69\% & 17.95\%\\ 
\hline
\end{tabular}
\end{center}


***Applied for SBST 2022 competition***

################################################################################

# 

<!-- columns -->
:::::::::::::: {.columns}
::: {.column width="70%"}
![](transition)
:::
::::::::::::::


################################################################################

# Java standard library support at the end of 2020

\vspace{10mm}
\begin{overprint}
\onslide<1>\includegraphics{oldRT2}
\onslide<2>\includegraphics{oldRT2}
\end{overprint}

\pause

*Many of the standard library methods and classes can be approximated*

<!-- * Java standard library is used almost everywhere
* despite having access to standard library sources,
Kex struggles to simulate it
* many of the standard library methods and classes can be approximated
in SMT -->


<!-- ################################################################################

# Intrinsics library\footnote{https://github.com/vorpal-research/kex-intrinsics}

Intrinsics for basic operations and checks:

* `assertions` and `assumes`
* `unknown` values with no constraints
* array operations:
	* `contains` checks
	* array generation methods
* etc. -->


################################################################################

# Java rt-1.8 approximarion

Prototype implementation based on `kex-intrinsics`\footnote{https://github.com/vorpal-research/kex-intrinsics}:

* wrappers for primitive types
* string builders
* some collections (based on `ArrayList` approximation)
* utility methods from `Arrays` and `System` classes

Kex substitutes all Java runtime operations with approximated analogs if they are available

################################################################################

# Exmaple of `ArrayList::add` method

\small
```java
@Override
public boolean add(E e) {
  AssertIntrinsics.kexNotNull(elementData);
  int oldLength = elementData.length;
  elementData = CollectionIntrinsics.generateObjectArray(
  	oldLength + 1, 
  	index -> {
      if (index < oldLength) return elementData[index];
      else return null;
  });
  elementData[oldLength] = e;
  return true;
}
```


################################################################################

# Support of intrinsics

Predicate State level support:

* Kfg extended to support `invokedynamic`
* PS extended to support lambdas
	* lambdas can't change program state

SMT level support:

* slightly reworked memory model
	* arrays are now represented as SMT arrays
* $\exists$ and $\forall$ quantors for array operations
* $\lambda$ expressions for array generation
* experimented with SMT string theory (unsuccessfully)

<!-- ################################################################################

# SMT support of intrinsics

\vspace{10mm}
\begin{overprint}
\onslide<1>\includegraphics{oldMemoryModel}
\onslide<2>\includegraphics{memoryModel}
\end{overprint} -->


################################################################################

# Java standard library support: current state

* prototype implementation
	* limited in expressivness
	* limited number of supported classes
* no thorough evaluation
	* experiments show a small increase in coverage


################################################################################

# 

<!-- columns -->
:::::::::::::: {.columns}
::: {.column width="70%"}
![](transition)
:::
::::::::::::::


################################################################################

# Reanimator

* an approach to generate valid code snippets using
only public API
	* can’t produce invalid objects
* works in reasonable time
* applicable in any automatic test generation tool
* can be used in any programming language


################################################################################

# Reanimator at the end of 2020

* working prototype
* evaluation:
	* testing with Kex on open source projects from github
	* testing on random objects
* can successfully and efficiently generate 70% of target
objects on average

**Problem: evaluation is not representative enough**


################################################################################

# New evaluation

* integrated Reanimator as an optional test generation module in Tardis\footnote{Braione P., Denaro G. SUSHI and TARDIS at the SBST2019 Tool Competition //2019 IEEE/ACM 12th International Workshop on Search-Based Software Testing (SBST). – IEEE, 2019. – С. 25-28.}
	* extended Tardis model computation
* compared with its default test generator --- Evosuite\footnote{Fraser G., Arcuri A. Evosuite: automatic test suite generation for object-oriented software //Proceedings of the 19th ACM SIGSOFT symposium. – 2011. – С. 416-419.}
* used SBST 2021 benchmark for evaluation, but extended time budgets


################################################################################

# Reanimator: current state


\small
\begin{center}
\begin{tabular}{|r|c|c|c|c|}
\hline 
\textbf{}    & \textbf{60s} & \textbf{120s} & \textbf{300s} & \textbf{600s} \\ 
\hline 
\texttt{tardis + evosuite}  & \textcolor{OliveGreen}{13.96\%} & 15.71\% & \textcolor{OliveGreen}{18.50\%} & \textcolor{OliveGreen}{19.60\%} \\ 
\hline 
\texttt{tardis + reanimator}  & 13.84\% & \textcolor{OliveGreen}{15.99\%} & 17.84\% & 19.30\%\\ 
\thickhline
\texttt{kex + reanimator}  & 24.57\% & 25.29\% & 25.43\% & 27.61\%\\ 
\hline
\end{tabular} 
\end{center}



################################################################################

# Reanimator: what to do

* implement Tardis approach in Kex
* compare both Tardis and Reanimator on random object generation
* improve Reanimator (duh)
* try to publish as is


################################################################################

# Plans for the (nearest) future

* SBST 2022
* finish work on Reanimator
	* evaluation and publication
	* integrate Reanimator with concolic mode
* extend standard library support
	* support of state chages in lambdas
	* more classes


################################################################################

# Contact information

<!-- link -->
<abdullin@kspt.icc.spbstu.ru>

<azat.abdullin@jetbrains.com>

\vspace{15mm}

<!-- columns -->
:::::::::::::: {.columns}
::: {.column width="50%"}
![](jetbrainsResearch)
:::
::: {.column width="50%"}
\vspace{1mm}
![](polytech)
:::
::::::::::::::

################################################################################


<!-- 

################################################################################

# Kex related projects

* **Darya Grechishkina "Loop backstabbing for Kex"**
* **Vladislav Feofilaktov "Spider"**
* Petr Menshov "Effectiveness of paths search algorithms in Concolic Testing"
	* based on the prototype from Andrey Bychkov "Conteau: Concolic Testing Augmented"
* Golubev Kirill "SymFPU for Boolector"
* Viktor Korotkih "Interactive UI for Kex"
* Ramis Sahibgareev "Kfg pass manager" -->