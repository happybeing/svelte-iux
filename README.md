# Svelte iUX - Component Library

WORK IN PROGRESS - no useful code here yet!

**Svelte incremental UX (iUX)** is a library of experimental web components being used to explore ideas for improving the UX of a complex application: [Visualisation Lab](https://github.com/theWebalyst/visualisation-lab).

iUX components are created for use with the Svelte web framework and in a specific application, but the will I hope be transferrable, hence putting them in a library from the start.

#### Join The Discussion
I welcome discussion of the vision (below) and any collaboration on ideas, testing and code. So feel free to open an issue with questions or contributions of any kind.

#### Svelte and Svench
This component library uses **Svench**, an experimental workbench for building **Svelte** components, and was made by taking a copy of the [svench/example](https://github.com/rixo/svench/tree/master/example) directory and modifying that.

# Svelte iUX Vision

None of this is code yet, it has literally just emerged from my head. I haven't even drawn any pictures except in my head (sorry!).

## Revealer Controls

**Svelte incremental UX (iUX)** provides a set of 'revealer controls' as Svelte
components. The idea is that by co-ordinating these controls you can build a UI
which 'unfolds' the story or workflow of an application incrementally for the
novice user, without impeding the expert.

### iUX Aims and Approach

For the new user, the approach is to reduce complexity and introduce 
features gradually, avoid overwhelm, increase retention and ease learning. The approach explored by iUX is to:

- Minimise the amount of information presented at any stage, keeping to what is
necessary for the current stage or to move onto a new stage or a different task.

- Reveal features in a way which guides and tutors, by showing controls and
optional follow on stages relevant to the current context and workflow stage.

- Unfold the application UI incrementally according to typical workflows,
re-enforcing the stories of different use cases, their relation to
application features and to the corresponding areas in UI.

- Give the user control over the process so they can move forward, back and
  around at their own pace, or dig deeper into the complexity of any stage.

- Ensure a knowledgable user can get directly to any feature and navigate
around the UI just as easily as if all the complexity were presented
at once in a user interface they know very well. 

Trying to cater for the novice and expert is particularly ambitious as if the former wasn't enough. So to make life a little easier the focus will be on helping the novice, while keeping catering for the expert in mind as an important longer term goal.

## iUX Component TODO List
This is the first take on what iUX components may be needed and how they can fit together:

- [ ] **IUXContainer** - holds a sequence of major stages in application workflow with some control over how the subcomponents are laid out

- [ ] **IUXTabContainer** - holds a sequence of high level, distinct stages

- [ ] **IUXRevealContainer** - to sequence or manage complexity of subsets of UI within a higher level stage

- [ ] **IUXRevealArea** - hols a small set of related HTML or general Svelte UI controls

## iUX Component Summary
In the hierarchy of application structure and workflow we have the following iUX components. 

- IUXContainer
  - IUXTabContainer
    - IUXRevealContainer
      - IUXRevealArea

In this preliminary design, other HTML and Svelte UI components will be children of the **IUXRevealArea**. 

The purpose of each of these compnents:

- **IUXContainer** - holds a sequence of major stages in application workflow with some control over how the subcomponents are laid out

- **IUXTabContainer** - holds a sequence of high level, distinct stages

- **IUXRevealContainer** - to sequence or manage complexity of subsets of UI within a higher level stage

- **IUXRevealArea** - hols a small set of related HTML or general Svelte UI controls

Restrictions on which iUX components can be contained by which other iUX components are not laid down at this stage. The intention is to support a tree or graph like structure which can be adapted to a variety of application styles and workflows. So there will be a need to have 'higher' level elements contained by 'lower' level components rather than make following the above hierarchy mandatory.

### IUXContainer
**iuxContainer** is a container for other iUX controls and can be used to create groups and a hierarchy, which provides the high level structure and flows of the user experience (UX). Provides control over how groups of control, probably always of the same type, are arranged within the container.

### IUXRevealContainer
**IUXRevealContainer** is a simple control which can reveal UI controls in an 
area that was previously blank. Multiple regions can be managed within the overall area managed by this control, intended to be shown or hidden in one or more patterns. Example patterns: 
- **telescope**, where each reveal extends by showing the next region in the sequence, or hides by reversing the sequence
- **concertina**, where only one of the areas in the sequence is visible at a time, and the user can easily move forward and backward through the sequence. An option will be to indicate the individual sequences and enable the user to click to reveal one without having to visit the intermediate areas in the sequence.

A design choice left to the developer will be whether the changes should affect the size of the area used by the **IUXRevealContainer**. It may be tempting to try too many regions or allow too much disruption when using this control, and defaults which have to be consciously overriden by the developer can be used to discourage that once some sensible guidelines have been established. With this in mind, the default will be for an area which does not change size as regions within it are shown or hidden. The default will be to minimise disruption to the user's visual model of the UI.

Options may allow for differenet styles of visual clues and features such as area boundaries and navigation controls, according to the degree of structure to be emphasised, navigation features made available and so on.

Options may select different behavious, such as to operate horizontally or vertically, or in different directions such as left to right, or right to left etc. Or to follow different paths as in left to right + down + left to right, or left to right + down + right to left.

### IUXRevealArea
**IUXRevealArea** will I imagine will be necessary to define the areas being managed by an **IUXRevealContainer** component.

### IUXTabContainer
**IUXTabContainer** is a component for managing multiple areas of UI, such as a set of **IUXRevealContainers**. 

It is similar to a traditional tabbed UI, but instead of *switching* between
tabs, it *slides* each tabbed area in or out to reveal or hide its UI. This is similar to the resizing mode of **IUXRevealContainer** and might in fact be implemented using common code, but for now it has a separate name. It might anyway be easier for developers to understand if this distinction were to be kept, regardless of how it is implemented.

It will probably need similar control options as those outlined for the **IUXRevealContainer**, but will have a distinct appearance appropriate for a higher level stage.

### Related UI Techniques

The above ideas aren't new, but I think are more ambitious in my conception than
the existing techniques that I'm aware of. I am though not up-to-date with state
of the art in this area, so only offer a couple of examples of current practice
and invite you to point out others to help inform or improve.

Two examples of current practiceare the 'Advanced' button which reveals
additional UI, and the 'Wizard' to guide through a procedure. These help, but
can't satisfy the ambtions of iUX as explained below!

#### Advanced`>` / `<`Hide
Some UIs reduce complexity and overload by showing only a subset of UI for
controlling a feature, and revealing more UI controls when the user clicks a
button such as 'Advanced'. This reveals additional UI, which can be hidden again
if desired. 

It is this simple idea which iUX builds on, and attempts to bring to an entire
application. The principle appears in other areas such as toolbars or menus which can be configured for simple or advanced users and so on. But ironically this can add to complexity at the same time, and create problems when an option is hidden from the novice rather than offered at just the point they might be ready to learn about it.

In iUX, the idea is to do this in a way which is intuitive for a new user and
facilitates learning the application's capabilities, workflows, and UI
structures. And to do so without getting in the way once this has been fully
understood.

#### Wizard Dialog
Another technique which I think has been around even longer than Advanced/Hide
is the Wizard Dialog: a sequence of pages with 'Next' and 'Back' buttons. 

A Wizard is useful for configuring a feature or initiating a task with complex
starting conditions. But a Wizard is also limiting and restrictive so only
useful for subsets of a complex application. This doesn't work well in
applications with multiple features which are intended to be used in
combination, which is an example of the extra yard that iUX is going for.

#### Progressive Disclosure
As if by magic after sharing the first version of this vision, someone kindly
introduced me to the term "progressive disclosure" and a quick search reveals a
few examples (below). 

- Mega Menus
- Overlays and Popovers
- Hover Controls
- Sliders and Carousels
- The Hamburger Menu
- The Read More link
- Pagination
- Accordian Elements

On first examining examples of the above I was underwhelmed, which at least means there's plenty of scope to improve things in this area. This of course doesn't mean I can do any better :-). If you want to see for yourself what the above refer to, see "Sources" (below) which include examples of each.

Some of these are even pet hates of mine! Mega Menus for example get in my way until I learn to discard my preferred way of moving the mouse and conform to something which is being imposed.

I think there's an issue not with the technology here, but the motives of those
for whom it is being deployed, and the metrics which they choose to optimise.
Much of the badness in online UX is actually an attempt to control and direct
users for the benefit of service providers, rather than empower and inspire the
user for their own benefit. For example, in the sources I used for the above I
notice that *progressive disclosure* design principles implore developers to
provide the user with information as needed, while the execution is, in my
day-to-day experience designed to push or funnel my activity for the benefit of
a service provider.

This is known as a "dark pattern". Today I saw *frictionless UI* being labelled as a dark pattern ([Frictionless is antithetical to autonomy](https://mobile.twitter.com/i/status/1262881162839699456), 22 May, 2020), and was given pause because I think frictionless UI is a quality I'm striving for in iUX!

The issue though is not with the technology (progressive disclosure or frictionless UI), but with the incentives of those using it and the choices they make when implementing it. Phew!

Sources for the "progressive disclosure" techniques listed above:
- *Use Progressive Disclosure to Simplify Complexity*, by Jeff Dance ([May 2017](https://www.freshconsulting.com/uiux-principle-51-progressive-disclosure-hides-complexity/))
- *The art of progressive disclosure in web design,* by Waldo Broodryk ([August, 2016](https://webflow.com/blog/the-art-of-progressive-disclosure-in-web-design))

## iUX Techniques
Some embrionic ideas for techniques that I think will help support the aims of iUX:

- Use transitions when expanding or contracting, revealing or hiding, which
minimise disruption to the rest of the UI, and so to the user's mental
model of the system. For example, by not re-arranging existing controls or areas except for specific purposes, see next.

- Rearranging is necessary at certain points in a dynamic, unfolding UI, but it can also be useful. For example as a signal that a new stage in the story is being revealed rather then a step made within the current stage. It may be a signal of completion, and for a well earned pause with an implied pat on the back. And still a rearrange must be done with minimum disruption to the status quo. There's a whiff of gamification here, but for now I don't plan to use that approach overtly because iUX philosophy is to enable choice rather than impose a direction.

- Provide subtle, consistent visual clues which reflect the stages, such as
shades or boundaries separating areas of incremental change, rather than
simply adding to a homogenous controls area.

I welcome discussion and comment on any aspect of iUX, so feel free to open an issue for that, or to ask a question etc.

## Using Svench

Svench is at this time still a proof of concept and so subject to change, but
some basics on using it are included below. For latest information on Svench or
more details on the configuration files see the [Svench
github](https://github.com/rixo/svench/).

### Prerequisites
- `node` v12

This project has a `.nvmrc` file for use with `nvm` to select `node` version which can be used as below:

### Setup
```bash
git clone https://github.com/theWebalyst/svelte-iux.git
cd svelte-iux
nvm use
yarn
```

### Start Svench

```bash
yarn svench
```

Open http://localhost:4242. Edit / add things in `./src`.

### Using Svench

Each component has a '.svench' file next to the '.svelte' component file, and this file is used by Svench to create its menu, and provide the context for
displaying and interacting with the component.

For more on these, see the [Svench README](https://github.com/rixo/svench/).

## LICENSE
See LICENSE. 

TL;DR:
- **Svelte iUX** Compnents are under GPLv3
