---
label: Sandbox
rank: 3
$rocket: ::icon{name="Rocket Launch" weight="bold" fill="true"}
---

# {{rocket}} Block Playground
### A quick tour of the new content blocks


## Callouts

::callout{type="info"}
This is an **info** callout — great for context or links.
::end

::callout{type="tip" icon="auto_awesome"}
A **tip** callout with a custom icon.
::end

::callout{type="warning"}
A **warning** callout for caveats.
::end

::callout{type="danger"}
A **danger** callout for things to avoid.
::end


## Badges

I work with ::badge{label="React" color="info"}, ::badge{label="Python" color="tip"},
::badge{label="PyTorch" color="danger" icon="local_fire_department"} and
::badge{label="SQL" color="secondary"} on a regular basis.


## Columns & cards

::columns

::col

::card{title="Machine Learning" icon="neurology"}
Model training, evaluation, and deployment.
::end

::end

::col

::card{title="Data Science" icon="insights" href="https://example.com"}
Analysis, visualization, and storytelling. (This card is a link.)
::end

::end

::end


## Code highlighting

```python
def greet(name: str) -> str:
    # syntax highlighting via highlight.js
    message = f"Hello, {name}!"
    return message

print(greet("world"))
```


## Gallery

::gallery{images='["https://picsum.photos/seed/portfolio1/900/560", "https://picsum.photos/seed/portfolio2/900/560", "https://picsum.photos/seed/portfolio3/900/560"]' width = "500"}


## Embed


::embed{type="youtube" id="jNQXAC9IVRw" title="Demo video" width="500" align="start"}


## Bookmark

::bookmark{href="https://github.com/guptadhruv-dev" title="My GitHub" description="Open-source work and project repositories." image="https://picsum.photos/seed/ghcard/240/240"}


## Cross-references

Jump back to ::ref{to="education" label="my education" icon="north_east"}, or to
::ref{to="fav-course" label="my favourite course"} further down this section.

My favourite course is ::anchor{id="fav-course" label="Applied Machine Learning"} — clicking the reference above scrolls here and highlights it.
