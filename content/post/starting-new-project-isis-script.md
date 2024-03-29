+++
title = "Starting a new project - Isis Script"
date = "2015-06-04"
tags = ["isis", "dsl", "eclipse", "xtext", "sculptor"]
aliases = ["/2015/06/04/starting-new-project-isis-script.html"]
+++
Recently I found some spare time to look into [Sculptor](http://sculptorgenerator.org/) issue [#158](https://github.com/sculptor/sculptor/issues/158). Here [Dan Haywood](https://github.com/danhaywood) suggested to add support for the [programming model](http://isis.apache.org/documentation.html) of [Apache Isis](http://isis.apache.org/).

The DSL outlined in JIRA ticket [ISIS-369](https://issues.apache.org/jira/browse/ISIS-369) shows similarities to [Sculptors DSL](https://github.com/sculptor/sculptor/blob/master/sculptor-eclipse/org.sculptor.dsl/src/org/sculptor/dsl/Sculptordsl.xtext): It has persistent entities (with properties/attributes and actions/operations) and services (actions/operations). The Isis DSL defines a model with objects which can be directly mapped to fully functional Java objects (with annotations and code blocks). But Sculptors DSL defines a model which has to be [enriched and transformed](http://sculptorgenerator.org/documentation/developers-guide#transformations) first before code or configuration can be generated. And the code generated by [Sculptors Maven plugin](http://sculptorgenerator.org/documentation/maven-plugin) is not complete - in the generated [gap classes](http://sculptorgenerator.org/documentation/advanced-tutorial#gap-class) the business logic has to be implemented manually.

So it seems that (instead of leveraging Sculptor) creating a new Isis-specific DSL is the way to go here. As suggested in the JIRA ticket this DSL should use [Xtexts](http://www.eclipse.org/Xtext/) support for [Java types](https://www.eclipse.org/Xtext/documentation/305_xbase.html#jvmtypes) and [Xbase expressions](https://www.eclipse.org/Xtext/documentation/305_xbase.html#xbase-expressions) to generate Java code.

I've been keen on fiddling around with Xbase for a while. This gives me a perfect reason to do some serious experiments with a Xext DSL which uses Xbase. To document the results I'm starting a new project - Isis Script. The source code can be found in the GitHub repository [vaulttec/isis-script](https://github.com/vaulttec/isis-script).
