+++
title = "Isis Script - First (alpha) version available"
date = "2015-07-06"
tags = ["isis", "dsl", "eclipse", "xtext"]
aliases = ["/2015/07/06/isis-script-first-alpha-version.html"]
+++
As mentioned in my [previous post]({{<ref "/post/starting-new-project-isis-script.md">}}) I've been working on a DSL for [Apache Isis](http://isis.apache.org/). Now a first (alpha) version is finished. This version provides a very early stage of an [Eclipse editor](https://github.com/vaulttec/isis-script#the-eclipse-dsl-editor) for the [Isis Script DSL](https://github.com/vaulttec/isis-script#the-dsl).

![Isis Script DSL Editor](/images/isis-script-first-alpha-version/simpleobject-dsl-editor.png)

Only a few features are available yet:

 * entities with injections, properties, events and repository
 * services with injections
 * a simplified version of action for entities and services

This feature set allows us to implement the entity and repository of the domain object `SimpleObject` from the [Apache Isis Maven archetype SimpleApp](http://isis.apache.org/guides/ug.html#_ug_getting-started_simpleapp-archetype). The corresponding Isis Script (based on the current version of the DSL) is as follows:

{{< highlight java >}}
package domainapp.dom.modules.simple

import javax.jdo.annotations.Column
import javax.jdo.annotations.DatastoreIdentity
import javax.jdo.annotations.IdGeneratorStrategy
import javax.jdo.annotations.IdentityType
import javax.jdo.annotations.PersistenceCapable
import javax.jdo.annotations.Queries
import javax.jdo.annotations.Query
import javax.jdo.annotations.Unique
import javax.jdo.annotations.Version
import javax.jdo.annotations.VersionStrategy
import org.apache.isis.applib.annotation.Action
import org.apache.isis.applib.annotation.ActionLayout
import org.apache.isis.applib.annotation.BookmarkPolicy
import org.apache.isis.applib.annotation.DomainObject
import org.apache.isis.applib.annotation.DomainObjectLayout
import org.apache.isis.applib.annotation.DomainServiceLayout
import org.apache.isis.applib.annotation.Editing
import org.apache.isis.applib.annotation.MemberOrder
import org.apache.isis.applib.annotation.Parameter
import org.apache.isis.applib.annotation.ParameterLayout
import org.apache.isis.applib.annotation.Property
import org.apache.isis.applib.annotation.SemanticsOf
import org.apache.isis.applib.annotation.Title
import org.apache.isis.applib.query.QueryDefault
import org.apache.isis.applib.services.i18n.TranslatableString

@PersistenceCapable(identityType=IdentityType.DATASTORE)
@DatastoreIdentity(strategy=IdGeneratorStrategy.IDENTITY, column="id")
@Version(strategy=VersionStrategy.VERSION_NUMBER, column="version")
@Queries(#[
	@Query(name = "find", language = "JDOQL",
		value = "SELECT FROM domainapp.dom.modules.simple.SimpleObject"),
	@Query(name = "findByName", language = "JDOQL",
		value = "SELECT FROM domainapp.dom.modules.simple.SimpleObject WHERE name.indexOf(:name) >= 0")
])
@Unique(name="SimpleObject_name_UNQ", members = #["name"])
@DomainObject(objectType = "SIMPLE")
@DomainObjectLayout(bookmarking = BookmarkPolicy.AS_ROOT)
entity SimpleObject {

	@Column(allowsNull="false", length = 40)
	@Title(sequence="1")
	@Property(editing = Editing.DISABLED)
	property String name

	event UpdateNameDomainEvent

	@Action(domainEvent = UpdateNameDomainEvent)
	action updateName(@Parameter(maxLength = 40)
            @ParameterLayout(named = "New name") String newName) {
		setName(newName)
		this
	}

	action default0UpdateName() {
		getName()
	}

	action validateUpdateName(String name) {
		if (name.contains("!"))
			TranslatableString.tr("Exclamation mark is not allowed")
		else null
	}

	@DomainServiceLayout(menuOrder = "10")
	repository {

		@Action(semantics = SemanticsOf.SAFE)
		@ActionLayout(bookmarking = BookmarkPolicy.AS_ROOT)
		@MemberOrder(sequence = "1")
		action listAll() {
			container.allInstances(SimpleObject)
		}

		@Action(semantics = SemanticsOf.SAFE)
		@ActionLayout(bookmarking = BookmarkPolicy.AS_ROOT)
		@MemberOrder(sequence = "2")
		action findByName(@ParameterLayout(named="Name") String name) {
			container.allMatches(new QueryDefault(SimpleObject,
				"findByName", "name", name))
		}

		@MemberOrder(sequence = "3")
		action create(@ParameterLayout(named="Name") String name) {
			val obj = container.newTransientInstance(SimpleObject)
			obj.name = name
			container.persistIfNotAlready(obj)
			obj
		}
	}

	title {
		TranslatableString.tr("Object: {name}", "name", name)
	}
}
{{< / highlight >}}

Technical details of the implementation can be found [here](https://github.com/vaulttec/isis-script#the-implementation).

If you're interested in giving the [Eclipse editor for Isis Script](https://github.com/vaulttec/isis-script#the-eclipse-dsl-editor) a try then you can find the corresponding information [here](https://github.com/vaulttec/isis-script#installation). 
