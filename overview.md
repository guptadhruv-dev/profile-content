---
label: Overview
rank: 1
work: ::icon{"Work"}
---

# Hey, I'm Dhruv!
## {{work}} I'm a Software Engineer & Data Scientist


> <div style = "font-size:20px"> <b>My Philosophy</b> <br>
> Measuring a project by its lines of code is like measuring an aircraft by its weight.
> </div>


::toggle { default = "open" }
## Background


#### Industry

- **Software Engineering Intern at Sallie Mae**: Engineering
  enterprise data pipelines and AI tooling in a cloud-first production
  environment.
- **Co-founded a Software Consultancy**: $50K+ Revenue, five-person team, 80%
  client retention, built while finishing a degree.
- **Full-Stack Engineering across Mobile & Web**:
  Shipped iOS, Android, and web products with 100K+ monthly active users
  across multiple production environments.

#### Academia

- **MS in Applied Data Science, Indiana University Bloomington**: Core expertise in Deep Learning, Applied ML, and Computer Vision; Trained and evaluated large models on HPCs.
- **B.Sc. in Computational Mathematics & Statistics**: The foundation that lets
  me derive a loss function, not just tune one.
- **Research Excellence**: Original undergraduate research into LLM
  parameter behavior using Google PaLM 2, 85% query resolution rate.

::end

## What Drives Me

My best work happens when a question refuses to let me go. For a parking
occupancy system, the obvious question is *"does it work?"* The question
that kept me up was *"how much labeled data do you actually need before it
works?"* So I drew a supervision curve from zero labels to full supervision
across 493,000 parking space crops on an A100 cluster and in the process,
found annotation errors in a decade-old benchmark dataset that the model was
confidently correcting. The reported accuracy numbers were pessimistic. The
model was right. That kind of result doesn't come from running the assignment.
It comes from not knowing when to stop digging.

The same instinct shows up in the architectural work. When I need a model to
be permutation-invariant, I want a mathematical guarantee, not a hope. So I
built it from symmetric functions and then proved the invariance by
collapsing three shuffled input variants onto a single point in embedding
space. The PCA plots don't just show accuracy. They show the geometry of
the guarantee.

::columns
::col
::card{"Mathematical Rigor", icon="function"}
Computational Mathematics taught me to read a proof and translate it into
working code. I move comfortably from deriving a loss function to fine-tuning
a ResNet on a GPU cluster and I know the difference between a result that
holds and one that just appears to.
::end
::end
::col
::card{"Proven Results", icon="monitoring"}
A 3TB data pipeline I built for Innoeversity serving 50K+ daily users, a
parking occupancy study trained on 493K crops at 99.75% accuracy on an A100
cluster, mobile apps with 100K+ monthly active users. Real scale changes what
you think about. I've had that education.
::end
::end

::end


## My Technical Biases

I've done most of my serious ML work in **Python** and most of my native
application work in **Swift**, **TypeScript** and **JavaScript**. Swift, in my view, is the most
architecturally coherent platform I've worked with. The consistency across
Apple's ecosystem is something you have to experience to fully appreciate. I'm also learning **Rust**, not because it's fashionable, but because its model of correctness-by-design has quietly changed how I think about reliability in everything I build.