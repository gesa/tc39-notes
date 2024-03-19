100th TC39 Meeting
8th Feb 2024
Delegates: re-use your existing abbreviations! If you’re a new delegate and don’t already have an abbreviation, choose any three-letter combination that is not already in use, and send a PR to add it upstream.

You can find Abbreviations in delegates.txt

**Attendees:**
| Name                   | Abbreviation | Organization      |
| ---------------------- | ------------ | ----------------- |
| Istvan Sebestyen       | IS           | Ecma              |
| Chris de Almeida       | CDA          | IBM               |
| Ron Buckton            | RBN          | Microsoft         |
| Kevin Gibbons          | KG           | F5                |
| Gus Caplan             | GCL          | OpenJS Foundation |
| Jordan Harband         | JHD          | HeroDevs          |
| Richard Gibson         | RGN          | Agoric            |
| Philip Chimento        | PFC          | Igalia            |
| Nicolò Ribaudo         | NRO          | Igalia            |
| Daniel Ehrenberg       | DE           | Bloomberg         |
| Linus Groh             | LGH          | Bloomberg         |
| Daniel Minor           | DLM          | Mozilla           |
| Eemeli Aro             | EAO          | Mozilla           |
| Justin Ridgewell       | JRL          | Google            |
| Rodrigo Fernandez      | ROF          | ServiceNow        |
| Bradford C. Smith      | BSH          | Google            |
| Daniel Rosenwasser     | DRR          | Microsoft         |
| Ross Kirsling          | RKG          | Sony              |
| Jan Olaf Martin        | JOM          | Google            |
| Ben Allen              | BAN          | Igalia            |
| Shane F. Carr          | SFC          | Google            |
| Kyle Barron-Kraus      | KBK          | ServiceNow        |
| Ethan Arrowood         | EAD          | Vercel            |
| Michael Saboff         | MLS          | Apple             |
| Duncan MacGregor       | DMM          | ServiceNow        |
| J. S. Choi             | JSC          | Invited expert    |
| Mikhail Barash         | MBH          | Univ. Bergen      |
| Samina Husain          | SHN          | Ecma              |
|                        |              |                   |
## call for reviewers for Promise.try
Presenter: Jordan Harband (JHD)

- [proposal](https://github.com/tc39/proposal-promise-try)
- [slides]()

JHD: Yes. So I – it slipped my mind to request reviewers for `Promise.try` the other day, so I would like volunteers for people to review `Promise.try`.

RGN: I’m in. 

RKG: I'll do it.

JHD: Thank you, RKG. And RGN. Anyone else is welcome, but I will start with those two. Thanks.
### Conclusion
* RGN and RKG have volunteered to review.

## Intl.DurationFormat stage 3 update
Presenter: Ben Allen (BAN)

- [proposal](https://github.com/tc39/proposal-intl-duration-format)
- [slides](https://notes.igalia.com/p/nxMdcUtbb/)


BAN: Okay. So for this update, there are no normative changes, with an asterisk we'll get to in the end. We finished up a fairly large refactor of more or less the entire spec based on feedback from implementers about it being just difficult to implement. So let me go through with the slides.

BAN: So the first thing is as I said, the current version of the proposal has an AO partial duration format pattern. That we have received a lot of frustrations from implementers about it. Most are related to how when we’re formatting durations, there are two kinds of radically different ways that we can format it:
- One is the standard way, which lists out each unit with designators for what the units are. For example, "one day, one hour, one minute and 100 microseconds", something like that.
- There’s a separate way of formatting, which is equivalent to the formatting you would find on a digital clock. So instead of using words… so this is the – where I am saying `{style: "short"}`, this is sort of how most styles work. So if it were not "short", if it were "long", it would say "hour", "minute" and "second", rather than "hr", "min" and "sec". That’s the way using anything but what I am referring to as numeric-like styles, that’s how it works. Numeric like styles, it looks like a digital clock, the bare numbers separated by separators, the appropriate separators. This is if this were a locale that uses a different separator, it uses the different separator.

BAN: Because these are so different, it makes more sense or is sort of easier to implement if we handle them separately. Previously, we had been handling it in one very large AO. With lots of special cases for if the specific unit we are formatting is numeric-like rather than one of the other styles sort of like work with the numeric formatting that is important to note, is that once one unit has been formatted as numeric, in this case, on the slide, specified that hours should be formatting using the numeric side, once one unit has been formatting as numeric, the entire thing subsequent to that unit is going to have to be numeric.

BAN: So, for example, for this one, we throw, if I were to say, `{hours: "numeric"}` and then `{seconds: "short"}`. So once you go in a numeric style for the rest of the formatting of the duration, it remains numeric style. So yeah. It’s fundamentally a different task than formatting the other styles. The steps handling it are different and previously they had been interspersed. It requires keeping a ton the state, and just lots of special cases. So that’s sort of like the main thing. I’ve got a draft PR up for that. I will copy over the link shortly. (https://github.com/tc39/proposal-intl-duration-format/pull/185 ?)

BAN: So that’s the large editorial change to the DurationFormat proposal. At the start, I said that everything is editorial with an asterisk. And I think that’s the important thing to talk about in this update. So the asterisk is also considered and in the draft PR, it does add additional slots to better localize the numeric style or numeric-like styles in certain cases. Let’s see what order I put them in.

BAN: So this is the most common one. Previously, DurationFormat, when rendering things in that digital clock style, uses – can only allow the use of the same separator between minutes and – hours and minutes versus minutes and seconds. So, for example, here, we could not have the separator between minutes and seconds and between 2 minutes and 3 seconds. We currently cannot have those differ as the spec has written. There are some locales where they typically do. The one that comes to mind off the top of my head is fr-CA, where typically, the separator between hours and minutes is H, and if there’s a separator between minutes and seconds, it’s an M or Min. So the refactor is not like crucial refactor, it can be split out, introduces [[HoursMinutesSeparator]] or [[MinutesSecondsSeparator]] internal slots so it is possible to use CLDR data or something equivalent to CLDR to respect the localization for this display.

BAN: The other similar one is it’s typical to use 2 digits for the hour in a time. Even if it’s only one digit long. So 07 and instead of 7. We have added an [[HoursDigits]] slot to again pull in data to actually respect this formatting for this locale. There’s several others, but Afrikaans is the largest one. But it’s editorial with the question mark.

BAN: So we are Stage 3. And this can be interpreted as a normative change, and also a bugfix. Because if we don’t have these, we will be doing localization wrong for some locales. So there are notes about how currently these separators to maintain the behavior that we currently have, that must be the same and a note saying these are added to maintain the current behavior – it’s stage 3 this can be interpreted as a normative. It provides a note saying, by the way, these have to be the same for now. Reasoning, it's avoiding the major normative changes. But as I said, these are about correctly formatting changes for all locales. You can interpret the wrong formatting for French, as actually just a bug with DurationFormat.

BAN: Okay. And that is it for the slide set.I welcome feedback on this potentially bugfix, potentially normative change business going on with this. And while I am waiting for questions, I will copy in the link to the draft PR.

KG: Yeah. This seems like a good change. I think it’s fine to make the normative change at this point and say, like, we can just do the right thing. It happens that sometimes we miss something, especially with localization given how complicated it is. Relaxing it at this point is good. 

BAN: That’s fantastic to hear

DE: The PRs sound good to me. Are there any further issues that you are aware of for DurationFormat? Or does this resolve all remaining ones? 

BAN: I believe it actually resolves all remaining ones. 

DE: That’s great to hear. That’s a great milestone to have reached.

BAN: We are still missing some tests. And we have got some decisions to make if we want to reflect recent changes in Temporal. But yeah, these are the – these are the meaningful changes, the meaningful issues still we’re dealing with. 

DE: Could you elaborate on the last one? Reflecting changes in Temporal? 

BAN: Yes. So, for example, Temporal recently provided limits on the – basically, it explicitly forbids using utilities above 2^53rd and we will reflect that change, but the current state of the spec doesn’t. 

DE: I imagine that that’s also – I see that also a bugfix that doesn’t require further discussion. Maybe today we would call for consensus on you implementing that bugfix. 

BAN: Okay.

DE: We have done this previously with Temporal, we agreed on this before the patch was fully done. 

BAN: Fantastic. 

SFC: Yeah. I just paste a link to the issue that BAN is referencing. And we have done a fairly good job over the last – since the last update of making sure that the bug tracker is properly labelled and that all the issues are properly, you know, triaged. So I think based on what we know, right now, Ben is correct that these two issues he presented plus this Temporal limits issue are the three open issues that could affect the proposal in its current state. There are other issues that are either tests or labelled for V2, which means we will do this in the future maybe, but not for the current proposal. So, yeah. It would be – maybe if we have enough time, maybe the chairs, if we have enough time in the slot we could go through this issue 157. 

PFC [on the queue]: Strongly support consensus on this in advance.

BAN: All right. So this is what – this is the issue as it stands. This – this is what we’re planning on implementing real soon. If I recall my conversations with PFC, recent conversations with PFC, there were some changes to fix bugs with how to handle this in Temporal, which we are going to largely pull in.

SFC: So, Ben, it looks we have a PR open this, number 173

BAN: That reflects the handling before the fix to the bug that ABL identified with the new behavior of Temporal. Which we have TG2 consensus for the old version, the only thing that needs to be done is update that to fix the bug that ABL fixed with the Temporal solution. 

SFC: I see. Okay. The pull request says we brought to the last TG1 meeting and it got consensus there. 

BAN: There we go. 

DE: So I don’t think you should have to come back to committee to lend the union of these two bugfixes, I suggest we ask for consensus today on, you know, if someone want to review the patch, I feel like we can defer reviewing that to TG2 because this is well understood. Can we call for consensus on that? Does anybody have any concerns?

BAN: All right. Sounds like we have no concerns. And just to confirm, this would be for the version without the notes saying that hours, minutes, separators and minutes, second separators have to be the same and that [[HoursDigits]] has to be one. So removing those notes, regarding this as a normative bugfix to accommodate those locales better, and moving on. 

DE: Okay. I was suggesting for this duration thing. We should get consensus on that. If we haven’t already. 

BAN: Yes. So I would echo DE on that. I am requesting consensus for both of those things.

CDA: Yes. Can we get some explicit support? 

SFC: In the notes we should list exactly what we got consensus on.

CDA: Yes. Ben, do you want to dictate for the notes? That would be helpful. 

### Conclusion
Consensus on
- Adding additional slots to DurationFormat to handle locales that always use 2 digital hours, and for locales that use different separators between units and seconds and hours and minutes. (https://github.com/tc39/proposal-intl-duration-format/pull/186)
- Incorporating the recent changes to Temporal that limit the range of values that can be used. (https://github.com/tc39/proposal-intl-duration-format/pull/173 + a not-yet-drafted bug fix parallel with Temporal)

## ESM Phase Imports for stage 1
Presenter: Guy Bedford (GB)

- [proposal](https://github.com/lucacasonato/proposal-module-instance-imports)
- [slides]()


GB: So I am going to present today ECMAScript model phase imports or ESM phase imports. And this is an extension of the work that we did on the phasing process for source phase imports. That was for the WebAssembly use case. We were looking at the current WebAssembly model today which doesn’t benefit from a lot of the nice features of the module system because of its dynamic instantiation. With the source phase, we can import this as an actual Module, get it object, an existing object, that we updated to extent from abstract model source, in TC39, and you can then instantiate it normally and get some nice guarantees of static analyzability, ergonomics, improved tooling support, and some security-related features.

GB: So this is currently at Stage 3 and progressing, whereas what we don’t have is a phase import for JavaScript itself right now. The question is what about JavaScript model with as and is this useful for JavaScript modules and what use cases can we solve with that and how does it fit into the overall module.
And so the use case that we would like to focus on is actually the worker instantiation and this is because it has very similar properties to what the problem was for Wasm, which is the new worker, it’s not ergonomic, it’s not a static capability, the string passed to `new Worker` is not a module specifier, but a path. It has limited tooling support. If you write exactly this [on screen], webpack will build it. Other build tools won’t. Esbuild won’t. Rollup you need a special plug in for it. If you are off the path and a different worker, the build tool will miss it. That also creates portability frictions, libraries are discouraged from using worker patterns because tooling won't necessarily support it as easily. And the idea is, if we support a phase for imports from JavaScript, we can solve that problem just like we did for Wasm and supporting importing this source from JavaScript statically through the ESM module system. That source is then loaded through normal module resolutions so you are getting module specifier resolution in the host. That represents the module to pass directly to the worker. Furthermore because we know it’s a module we don’t need to put the `{type: "module"}` into the `new Worker` invocation. We can get a much more ergonomic worker indication out of exposing the source phase for JavaScript modules.

GB: And this is just copying what we said for Wasm. If you cross out Wasm, we get a lot of benefits. Seriously, it’s very specifically the proposal is that the phase import for JavaScript modules can solve for these worker ergonomics and static guarantees and worker capability should be the constraining design space for JS module phases. Workers bring up problems around how you want to share modules and share various properties of modules. And so solving those as the sort of next foundational step within the modules harmony effort seems like the right direction. So that’s the overall proposal.

GB: One of the questions we have is, which phase we want to primarily specify when we specify this, just to solve this worker use case. And this is going to be part of the initial Stage 1 exploration, which is that we have these different phases that we could explore using. The source phase comes after resolution. In the phases model, the first thing you do is you resolve it and get a key that represents that module in the registry. And that’s the resolve phase. Immediately after that, you can fetch and compile and get the source. And that’s the source phase. After source phase is attaching the evaluation context. And then linking and then evaluation.

GB: So we could also potentially explore using an "instance" phase for the use case and pass an instance to the worker. There’s some tradeoffs to be considered in that. We are leaving that as a slightly open design question for now.

GB: To explain some of the tradeoffs, but this is part of the design exploration space, while instances represent the full linkage graph, they are actually linked – it represents providing every single module in that graph to the worker. And if you have got a Wasm module instance, with shared instances, you can actually share the instance fully. And so that could potentially make sense. For JavaScript modules we don’t have the shared semantics. So when you share an instance with the worker, you will be copying it and then you have to draw an equivalence relation of a graph of modules in one agent and the graph of modules in another agent. And creating that equivalence relation and maintaining it in a way that is well-defined is quite difficult. On the other hand, instances might be more amenable to pre-loading because they represent the entire graph. You could preload it. And sources don’t represent the full graph up front. Although a source can be associated with the resolution model because every phase in the phase import system has all of the information of the previous phase.
So when I have a source, I also know the resolved module key. So I can extract from the sources fully resolve module key and resolve relative specifiers relative to the source. And if I have got an instance, I can extract the source from it.

So the information that gets associated with these phase is progressively added to the information available. So when you pass a source through worker, you could potentially actually treat the resolution information as part of that transfer and that’s another thing that we want to explore within the proposal

GB: The overall scope is to
Solve for the ergonomics, portability, and security of the worker instantiation 
Specify a built in phase object for JS which associates the registry key, and that reflects the module.
Support the module harmony layering, so the object we specify can be used by module expressionion, module declarations and other proposals in the effort
Investigate whether we want to be able to share the resolution model. 
Explore which phase we want to expose exactly. 
Whether we want to also specify post-message transfer for these objects. 
Whether we want to consider import source directly because it represents a key, so it could act as a capability for an import key that is static analyzable. 
The last thing is to consider reflection APIs.

So in the sense that we are enabling better static analysis of worker invocation in JavaScript, tooling is always doing analysis of module graphs and things like that. And we could very easily provide some primitives that make it easier to do module analysis in the language.

GB: So this is a topic I touched on briefly, but it’s sort of very closely related topic, which is right now, when you create a worker on the web, and you have an import map in the main page, the worker will not inherit the import map on the main page and there is in fact no way to set the import map for the worker. So what this means is that when you create a worker and load a module in the worker environment, any import map resolutions will not longer be supported for those worker modules.

There may well be something like this [on screen], this is just a shape of the proposal. There’s no current active efforts towards this direction. But in discussion, something like this has been discussed before, where you could pass in the import map to the worker invocation and have the ability to pass `{ importMap : "inherit" }` to share the import map from the main parent page context. Because there’s currently also no way to read off the import map from the top level page.

So the idea is here just like we were able to pass `{ type : "module" }` by default, it would be nice to pass ` { importMap: “inherit” }`  by default. And explore that as well.

And `{ importMap : "inherit" }` hasn’t been specified. But there may be a way to specify in ECMA262, even though it doesn't exist, based on the concept that a course carries a reference to its original host context and define that create a worker, you could potentially share the resolution context and define some concept of resolution context that could be set up for the worker agent when you create that worker and that might be a nice way to effectively define `{ importMap : "inherit" }` by default without it existing yet. When it does exist, we would support it. So that’s the other thing that would be nice to explore.

GB: And then finally, just to explain how this fits into the overall module harmony effort and story and the current proposals that we have. Import attributes: these apply to the initiation, so in the phase model, importer will be this applying at the first resolve phase. What that means is, that when you have a source, you’re going to already have the import attribute associated with it. If we were to support dynamic import of source, you wouldn’t override already provided import attributes and that’s something to define if we allow multiphase imports.

GB: Deferred imports are pretty much entirely orthogonal at this point. They represent the last phase. It doesn’t affect anything to do with the source phase.

GB: Module expressions. Depending on whether we define an instance phase or a source phase or both, module expressions are currently specified as an instance phase. They could potentially be specified as a source phase. They would likely have a getter to go from the module instance object, read off of it the `.source` to get the source object.
So we have layering in either of those directions. But they combine nicely with this proposal. Because you could basically just have `new Worker` off the module or the `module.source`. And it would allow working like this for workers which is much more ergonomic and solve a lot of the problems we have around data: URLs for workers today. So this layers nicely with module expressions, this is designed to be a first step towards module expressions, to get us towards a better ergonomics and they combine very well.

GB: As I say, there’s various design discussions to explore. But the layering working out however things play out is the important point.

GB: Module declaration, build on the same design of module expressions. And again, whichever way things go, they will layer with the ESM phase we define. Module declarations could be also currently defined as instances right now and could be defined as sources. In the sense that module declarations define a hard linkage between modules where you import from the module directly as an identifier. And you can’t break that linkage or virtualize that linkage. And that could defined for sources as it is for instances right now. As an extension of module expressions, they would behave in the same way as module expresses, in terms of how they layer with this proposal.

GB: And then finally, loaders. Loaders very much use the concept of module sources when they want to create instances. So there’s already a concept of source in the existing be compartments work. The nice thing about (?) There’s no dependence against loaders. It has utility and benefit entirely independently to loaders and it’s important that everything we do does not depend on a loader’s outcome. But everything we are doing is very much geared towards supporting loaders. If you want to create a custom module graph you can take a bunch of module sources and instantiate them and dynamically wire them together and build up a loader in that way.

GB: And here is a diagram of the layering that we have. At the top, the resolution phase. Module source is the nextment module instance in the linkage phase and deferred imports at the end of the evaluation phase.

We have worker interactions for the earlier phases that would potentially be transferable. And module source and module instance both feed into expressions and declarations, and then loaders layer last on top of the primitives and I think it’s a nice way for us to pick apart loaders because we are able to create the building blocks for loaders and eventually get the loaders at the end. If we get there, it will be a really nicely defined set of primitives.

Imports attributes and model sync attributes very much don’t have too much cross-layering. Exactly because import attributes are orthogonal.

I will open up to any questions or discussion. 

USA: Let’s give it a couple of minutes for the queue. But there’s nothing in there yet.

DE: That was a great presentation describing a pretty complicated area.
I think it’s important for the various reasons, guy stated, we move forward on exposing JavaScript modules that are not, you know, already imported at one of the earlier phases. I hope that within this, we can get a maximally simple interface.
For module declarations and module expressions, it will be really important to package that resolution context together with the declarations/expression. I previously thought that this meant we had to use instances. But if sources contain the resolution context, then that might also be a good way to do it. And it would provide us with a nice unification of all the concepts. Guy has successfully changed my mind about this. Anyway, good. 

USA: Thank you. Next we have SYG. 

SYG: Thank you for the presentation. I agree with Dan, it was a well-stated problem statement and summary of the details. And I want to express my support for the worker use case. I believe it’s an important use case. I have some questions about which phase to choose. It’s somewhat clear to me or I can imagine how it would work in a straightforward way, to postMessage the source phase. It’s not clear what it means to postMessage the instance phase. Do you have thoughts there for why that is even part of the - why it is even a choice for the open question? 

GB: So I guess there’s two indications to define. The one the direct `new Worker` – and we are only specifying a top-level working instantiation, you are passing in place of the current string path to the worker. So there’s that, and then the transfer question. So generic instance transfer is – you know, related to that, or you could imagine saying, for inFrance stance, they only support the new worker, but not arrest temporary transfer. If we do that, it’s similar to the source phase worker invocation because you’re passing at the top level.

But in both, the difficulty as I mentioned briefly is creating that equivalence relation between the registry keys in both agents, and ensuring that is consistent and not have differences. So you could imagine – first you need to make sure you share the resolution context, but then, you know, different modules could be loaded at different times. You can have dynamic resolution and failures in the one agent and success in the other agent of top-level module loads. There are inconsistencies that come up in defining that equivalence relation. We have discussed this at length in the modules group and there’s ways to do it but certainly a lot of weeds to avoid with sources.

LCA: So yeah. Quick clarification for the folks not aware, the reason that instance transfer is not possible is due to garbage collection because instances by definition sort of capture a bunch of objects and cross-thread garbage collection is not something that JavaScript engines can do or ever able to do.

SYG: That's not true.

LCA: Okay. Arbitrary cross thread -

SYG: Also not true.

LCA: OK, it is difficult to do. And we are not able to do it right now. Is that better? Okay. Shu agrees.

LCA: And as such, it is difficult to – when you post message an instance over to a different thread, and then pass it back to the main thread and pass it back to that thread to have that instance have the same identity on that thread without having a cross garbage collector. And because of this we are constraining ourselves – also discussions with YSV – to not go any direction where it would be necessary to have cross thread GC. If we say instance transfer, this would be a "clone serialized parts of the instance" rather than a transfer that maintains the identity.

DE: Yeah. I want to re-enforce what LCA said. This is kind of the point we got to a couple of years ago with respect to postMessage on module expressions, which is that it would only postMessage the source and some things about the resolution context, it would not post-message the module identity. We had meetings about this, which are captured in the notes. Tracking the identity back and forth is pretty – is too difficult . We reached that conclusion in the past. I think overall, this doesn’t really represent a difference between instances and source. Because in practice, we would just get the source and resolution context, do the transfer and reconstruct it on the other side.
If there’s – the resolution context is packaged with the source, as I think is necessary for whatever module expressions evaluate to, then there’s no difference that I am aware of, between instance and source in terms of post-message. 

SYG: I will pass on this. It looks like I will follow up with some folks off-line. 

LCA: We'll follow up off line. 

USA: Well, then the next topic is by Gus. 

GCL: Big + 1. The problem here is well defined and is something we want to solve. There is clearly a lot of stuff to work out.
I am interested to see how the embedding with HTML goes. Very positive and excited. 

KG: Yeah. Also excited for this. I just want to make sure that the other parts of the ecosystem are looped in early. In particular HTML, but also CSP. CSP is not super maintained, so it’s good to pull them in as early as possible. 

GB: The Wasm source phase very much has CSP interactions. So in the same way that CSP applies to Wasm, there should be some story there and that will be part of the – part of – certainly feedback taken on looping that in early. Certainly, we’re defining the object and then hoping that we can make that progress in HTML. So the – creating that compatibility and progressing those discussions certainly feedback taken. 

KKL: And I am in favor of Stage 1 for this. And really, thanks Gus for the excellent summary of the conversations we have had. I am leaning – I remain leaning towards having source not package resolution. But otherwise, I don’t recall where we settled on it some months ago. Looking forward to having conversations again. 

NRO: I would like to refresh with what – like, to remind you what we talked about months ago about this. One possible solution that we might have found was to split what we originally called resolution context into two different pieces. Like data and behavior, where data is, for example, the URL to resolve things from. And to put the data in the source, like we want to – like, proper to the private field and then have the – the behavior be in the instance so that you can still have source error roadway source data and realize how they are allotted . . . with custom loaders, that just read the data and decide if they want it use it, how they want to use it. But yeah, we will be happy to talk about this, more on that. 

KKL: Right. Yeah. That’s exactly the right thing to be thinking about, how this virtualizes if we get farther into the harmony proposals. Yeah. Thanks. 

DE: Another aspect here is that even if we do package things, we could still have virtualization-related APIs which taking a source that has the extra stuff, and disregard part of that and, you know, operate on or even return a different source that has different values or just do things with certain components of it. This is in fact what the post-message semantics for module instances would do if we went on the instance path. 

GB: One more clarification on the resolution context topic. Since we have these two invocations now, previously within the module group we always had a single way that you would import the source. And consider the resolution context being used. With the new worker style of worker creation, the worker creation is like – the process of creating the worker could be associated with picking up a resolution context that is provided to the creation as separate from the concept of just getting a source from a loader that you want to virtualize. So the difference between creating a loading environment and passing a source, whereas the source can provide the resolution context when you’re creating a loading environment is one way you might be able to split that. 

GB: And, yeah. I am going to formally ask for Stage 1. So yeah. Requesting Stage 1 for the proposal. And that would be great to get some support. 

USA: All right. I guess, KKL is the first supporter. Let’s give folks some time – or Nicolo, do you want to speak to that? 

NRO: Yes. Obviously, I am very involved in this case, but I support the proposal. If this goes ahead enough to give me the necessary building blocks, I will then start working on module expressions building on top of this. 

USA: Next we have support from DE and DLM on the queue. I would like to remind folks this is also the correct time to share any concerns that you might have. But it sounds like you have a strong consensus. Congratulations for Stage 1! 

GB: Thanks everyone.

### Speaker's Summary of Key Points
* List
* of 
* things

### Conclusion
* Stage 1
## Throw expressions update or stage 2.7
Presenter: Run Buckton (RBN)

- [proposal](https://github.com/tc39/proposal-throw-expressions)
- [slides](https://1drv.ms/p/s!AjgWTO11Fk-TkqMid5YKobUhLWZJkA?e=2xqwWq)


RBN: I wanted to circle back to a discussion that we had in the September plenary session, regarding the ThrowExpressions proposal.

And just for some – a brief– The idea of three expressions, this allows you to use the throw keyword to have exceptions thrown in situ within an expression context. This is not dissimilar with regular function calls in that functions can and produce a ThrowCompletion that results in the same type of logic that a three would. It fits right in within existing logic that we already maintain within the specification for how those completions are handled. One of the motivations, these are convenient. You don’t need a statement context to throw an exception. But also, the alternative to that is to use a function or method that you would invoke to throw an expression but that has issues with the debugging experience, especially if it’s not a built-in compatibility. So if you were to use a throw helper function, then you were having to deal with stack traces and consistency and capabilities with different engines, effects how debugging stops at break points and there are some other potential benefits for things like static analysis, control flow analysis as well.

So some history on the proposal: this was initially proposed and adopted for Stage 1 in July of 2017. This was due to overlap for do expressions and investigation in other statements as expressions in September 2017. This was proposed and adopted for Stage 2.

And in January 2018, we initially proposed it for Stage 3, at that time we were blocked by do expressions and separate syntax concerns. In the July 2023 plenary session wishings had a meta review and the throw expression came up. It was discussed and the do expressions was not considered at that time to be a blocking concern by the objector previously. And we were able to potentially resolve the syntax concern that was addressed or it was brought up with a series of pull requests in the September 2023 meeting, but we were blocked due to syntax concerns. I will go into those in a moment

RBN: Prior to the 2023 plenary, one of the biggest concerns with the syntax was the difference between the ThrowStatement and ThrowExpression precedence. In the ThrowStatement case, throwing 1, 2, evaluates the first expression of the comma, but throws the second expression. But throw is written as a UnaryExpression.

We chose UnaryExpression because it does – because the most predominant use case, throwing a new error or throwing an existing error object are satisfied by the UnaryExpression precedence without requiring excess by wrapping the expression parentheses or wrapping the nest parentheses. Tooling like minifiers might support ThrowExpression might do inlining in a way that looks different from how what you expect with a throw statement and could potentially be a hazard to those reading code


With a ThrowStatement. This would still maintain that unary statement but avoid potential user footguns where they have expectation of difference in precedence


And one of the other things that come up during the September plenary was appointed out by WH that there were certain restrictions or issues due to ASI – we put together PR #18 to address that. 


One of our primary rationales behind this being something viable is that the ASI, the rules used to express 

Given that it had been blocked previously by WH, one alternative we presented was one that not only mandated the use of outer parenthesis and left it as UnaryExpression so we would have the flexibility to relax that restriction in one direction or the other. But that approach was also blocked by WH at the time.

So we have gone back to the drawing board and considered two other alternatives to consider: so we still again want to maintain the statement precedence that ThrowStatement has for its expression within ThrowExpressions. Both require parenthesis. One we move ThrowExpression into expression. It’s already in the current spec text banned from being used in expression statements. So there is no conflict with a ThrowStatement in those cases. But by having it be nested in expression, that means that parentheses are only nested inside another one.

I will give an example of each one. In the first option, moving ThrowExpression into expression, this would require parentheses in almost all cases.
So pretty much everywhere you use an expression inside another expression, would result in needing to use parentheses.
However, if you were to use a ThrowExpression in the head of if statement with a while switch, the expression of a case statement, inside a ThrowStatement or in – as the top level, this would not require it. 

Better ways of expressing through code than those types of expressionsBy this would avoid that in those cases.

The second alternative we considered is only allowing ThrowExpression inside of a parenthesized expression. In this case, parentheses would be always required. So if for some reason you felt that you wanted to do the nonsensical thing of throwing in the head of an `if`, you would need to wrap in double parentheses.

Now, I will give a couple examples for these cases. The syntax legal in both cases, level in both is, for example, if I say parenthesis throw B, C, then it would have the semantics that ThrowStatement does. To parenthesis throw B and comma C, it throws B. In both of the expression, it would be illegal to not have a parenthesis in these cases
And again, going back to why this was originally in UnaryExpression precedent, illegal here that are scenical uses, it has a trailing throw, or more specifically, having the conditional expression here, that has the null coalesce expression, a trailing throw, these are cases that you might want to use parenthesis for, but now required in these – with this change. 

Versus regular expression is, everything here on the left would be essentially legal in both cases. But the things that are on the right would be illegal only if we chose Option 2, which is parenthesized expression.

So going back to all this, we were left with three options to consider as a way forward: one is Option 1, move ThrowExpression into expression. Another is to move ThrowExpression to parenthesized expression. And the third is to continue as is with the look ahead restriction. As champion, Option 3 is still my preference. I still believe that WH’s concerns at the time were related to spec complexity and I think that is something that is more within the domain of the editors to determine if they believe that that complexity is warranted. And assuming we are actually maintaining – maintain whatever semantics we chose for and the syntax we chose, if we’re to go with the existing version of the spec, that is today, we could write that actual specification text in multiple different ways, based on editorial feedback. But have the same syntactic rules going forward. I would like to discussion. We want to believe that the third option we have been discussing is something to consider using going forward. 

With that, I can go to the queue. I have had both my – my reviewers taking a look at both of the alternatives presented here as well as the versions we have presented in the past. So we have again 3 different directions to consider. So now I can go to the queue. 

KKL: Yeah. On behalf of Agoric, I can say that we strongly favor solving this problem without new syntax, given the price of new syntax.
And that we find a helper function to be a tolerable solution and sidesteps much of the issues. It’s possible to have a throw constructor method that is inherent to the other constructor errors that accepts the constructor arguments. As opposed to accepting an error constructed otherwise, which is ergonomically enticing. And worth consideration. That’s my piece. 

RBN: So to the second point that you made about prototype inheritance, that’s valuable to anything that inherent from the native error, JavaScript has no requirements to not throw B in error. It doesn’t satisfy that. Error.throw as a method to consider has been discussed in the past in this proposal. It is also discussed in summary form within the proposal explainer.

Our main rationale for not using a method is the problems with methods and functions when it comes to optimizing ability, static analysis, which has error throw as a function, then there’s the Prince Edward Island tension – but it has the – static analysis capabilities in pipe languages like TypeScript that can benefit. I have been discussing with SYG if there is any potential for effecting escape analysis. The way V8 works today, they don’t go to the level of death, but there is an opportunity for that in the future
 having it be a method or a function
does have complexity involved around the debugging experience works, you’re essentially have to
have debuggers informed about the blessed specific function or have a function have the
mechanism around stack phrases and frames and debuggability that isn’t unique to(

You were having to have debuggers informed about a blessed-specific function or a function have some specific mechanism to affect stack traces and a debugability. (switch) `Error.throw` and pull something off to make sure what I’m doing at run time is able to make thedecisions and adds a level of complexity isn’t there when we consider throw as syntax.
With throw as syntax it’s something that run times understand statements work and don’t require the type of complexity to interrogate whether this function when optimized is inlinable as just throw and do this type of complexities. It’s a perfectly viable selection for the debugging experience but in the actual feature in the language is not something that I would recommend.

KKL: I think that to respond to that, I think that the feeling around the water cooler is those
down sides are lesser than the down sides to syntax.

USA: We have a response by RGN.

RGN: Right. The first thing that you mentioned is that a hypothetical Error.throw couldn’t support
a non-error and I want to explicitly state that I don’t care. I’m positive on an approach that lacks sugar for throwing a non-error. You can already do it with existing syntax,
and I don’t think it needs to be made easy or that a proposal needs to be compromised in order to
support it.

RBN: I think you misunderstood. I wasn’t saying that error.throw wouldn’t work for non-error, but that the `Error.prototype.throw` inherited thing wouldn’t work for those cases.

RGN: That doesn’t change my position.

Next we have a new topic by Nicolo.

NRO: Yes. So just to make it clear to everybody here in the past we talked about potentially relaxing
parenthesis in the future, I believe if we go with option 1 or option 2 we have parentheses forever because the expression.

RBN: You essentially have it forever because the moment that we choose option 1 or option 2, we allow all expressions inside of throw, I’ll go back to an example of syntax for those. It’s just throw then expression.
As a result we can never restrict it to Uniary without breaking body in the world and in the September meeting we had a potential of this parentheses and UnaryExpression and that was allowed and the narrow focus and something that WH did not or was not willing to support.

LCA: Yeah, I want to echo again the points that I made in September already that I have a preference for option 3. I think the use case that you present with the double question mark and the default and argument function arguments motivates the case that you don’t want to wrap every throw in parentheses.

And I think these are probably the most common use cases here throwing when a default argument
is not present or when an argument is not passed. Throwing when something is null.
So I think we should make these use cases as simple as possible.If this increases spec complexity in some small amount it is a trade off that is worth it. The spec is read by much fewer people, then people that would read or write this code.

RBN: I also did not agree with WH’s perspective that increased complexity of the specification, I don’t have it in these slides or actually I have it in some hidden slides for reference but these were also presented in September that the actual spec complexity is essentially this. It’s early errors for three specific cases, or rather four because there’s a minor function that needs to be added to an SDO that
needs to be added to validate these cases and then the restrictions that we already discussed previously which was the trailing look ahead.
That actually isn’t that complicated of a change. The specification was built on the existing mechanism that we used to restrict optional chain template expression to avoid ASI that we had for several years now.

USA: Next we have mark.

MM: I just want to raise and I won’t take much time during this presentation for this.
But we need to treat this I think as an emergency as a committee because WH has shown
himself over and over again to be the only person who is able to in realtime spot problems with
syntax proposals because of the complexity of analyzing the implications of job description
syntax proposals with the look ahead and the semicolon insertion and just the weird not context
dependent lexical rules.
JavaScript is incredibly hard to figure out what problems are introduced by a syntax proposal
and WH has saved our butts over and over again and proved himself over and over again to
be the only person who could.
So I would like the committee to find some arrangement like permanent invited expert or something
or whatever it is that we did to permanently invite Brendan no matter what he is.
I think we need to do that with WH and I don’t feel comfortable accepting any syntax
proposal without seeing WH’s reaction to it.

DE: Yeah, WH has definitely been a valuable member of the committee over the years.
And he’s definitely found some syntax issues. I think that it would be unfair to say that, no, we don’t have anybody else in the committee who is capable.
It would be very unfair to say we don’t have anybody else in committee capable of also 
detecting these issues.
There are many people who implement and maintain these and work out the ambiguities and WH’s
style of reporting the issues that he found was very in the meeting and we found a lot of 
issues both before and after his analyses that weren’t reported with the same kind of brash,
hey, you find the error in the meeting.
So I think if we were in a place where we didn’t have implementer support and many different
people looking at the grammar would be an issue.
But I think we’ll be okay as a committee grammar-wise.

MM :What I meant to say and I’m not sure if I said it is that WH shown to be the only person who
can evaluate it in realtime.
He really has a genius level uncanny genius level ability to spot problems that I think is 
really, you know – I’ve just never seen anything like it.

DE: I think we should really be focusing on detailed offline review of these sorts of things.
And also clear explanations of what our goals are with grammar.

SYG: I’m also somewhat – to be clear I’m pretty neutral on the syntax versus the helper function
thing.
I admit I’m not fully convinced by the syntax use cases over a helper function but I don’t see
much harm for a language usability point of view. So I think for me basically comes down to which of the three grammar options is the simplest to
implement and the fastest by which I mean really that it’s the most localized, that there’s the
smallest likelihood that it will change parts and performance in any other way?
So which option would you say that is?

RBN: The option that’s likely to have the least impact on parts or performance most likely in my
opinion would be option 1.
Because it just is parsed as a top level expression with the single restriction on expression
statement.
That said, it’s also the least convenient for users, both option 1 and 2 are inconvenient for
the 95% use case which are the things that are – which are many of the things that are marked
as currently illegal on this slide. The 90% use case for ThrowExpressions is the right-hand side of a null coalesce operator, and in the true and false branches of a ternary, and initializer of the parameter.
Those are the places that are the most useful.
Also potentially useful in a concise error body, but that’s not – you can just write curlies
versus parentheses in those cases, it doesn’t really matter.
Those cases coalesce and conditional and parameters are the most likely conditions you see in
use.
And again there’s an issue with – my concern is user convenience when it comes to man dating
parentheses because that adds additional typing that you have to do for any place that you want
to throw.
It potentially unnecessary complexity and the 95% use case is throwing an existing thing or
throwing a new error.
You rarely will throw a comma separated list or throw A and B.
That can happen.
But again it’s not the majority use case.
And goal with the parsing restrictions was to have the ergonomics and convenience for users
with a hopefully limited effect on parsing since parsing can continue to parse as sequence of
tokens and as it producers the production says the left-hand side is the ThrowExpression and
you check is the right-hand side wants a set of specific tokens.
The only corner cases being ASI handling and I’m sure that how the spec is written to discuss 
ASI handling isn’t necessarily how parsers often will implement it if consistent with it and
easier and cheaper ways than we do it in the specification as well.

KG: I don’t think any of these options would affect the performance of parsing code that is
not using throw expressions. It is really an obvious token. So once you are in the branch where you’re parsing a ThrowExpression, option 3 is definitely a little bit more complicated.
But it’s like the difference between 20 lines in your parser and 5 lines in your parser.
Since it doesn’t affect anything outside of ThrowExpressions, I wouldn’t worry too much about
it.

USA: Next in the queue is Michael.

MF: I wanted to clarify a couple of things that are bothering me during this discussion. So the first thing is that I don’t believe option one requires more parentheses than option two. Option one requires more parentheses than option two, only if you do not change any of the current uses of the expression production to the new nested expression production. Option one gives us the opportunity to choose every single place where we want to not require parentheses. So that's just up to us. The most restrictive form of option one requires parentheses everywhere, but you can granularly, location by location, choose where not to use parentheses. So that's the first point I wanted to make, and that's why option one is strictly superior to option two. But I don't think that these should be the options in the first place. This is a false choice. The committee should be deciding which cases are important to write in which ways, and then leaving it up to the editors to figure out how to structure the grammar. We shouldn't be dictating a grammar or choosing a grammar and having some set of semantics fall out of that. That's just not how to do this design. Choose the things that are important and you want to write, and then the editors will figure out how to encode that in spec.

RBN: I agree.
I don’t have one clarification, now you seem to favour option 1 but in the discussions we had on the issue tracker, you seem to prefer option 2. I wanted to clarify what your preference was if we had to choose one of these.

MF: Which is the one that I’m looking at on the screen?

RBN: This is the one, I believe, that you had – that you preferred in the – of the two pole requests. That’s the one that you requested I changed it to use nested expression because originally was pren expression and pren ThrowExpression.

MF: You’re numbering this what?

RBN: This is option 2 on the screen. This is one that you expressed preference for.

MF: It's possible that I said things backwards when I was making my statements.
I apologize for that.

RBN: I just wanted to clarify which direction you were –

MF: I was numbering them incorrectly, sorry.

RBN: Yes.
Really it is a minor difference between options 1 and 2 here. It’s just a matter of do you want to allow it in certain nonsensical places? But this does show that one or the other of the actual spec for this is fairly simple. The specification necessary for option 3 which is kind of the status quo as the proposal is
right now and the fixes for ASI that we discussed in September is slightly more complicated from the spec text perspective. But I think it still provides the best usability for users.

NRO: I agree with Michael here that regardless of how this is, we can use granularly where we want option if it’s an option and double parentheses and if possible to specify that and the specs are similar and just a matter of choosing which places to put which double expression.

USA: Next in the queue is MM.

MM: So first of all just want to be clear. I think we have been clear. I just want to make sure that we’re clear that I didn’t find any of the arguments for syntax over helper compelling and based on that we would
not be willing to see this advanced as a syntax proposal.

We would support exploration of helper function proposals having a standard helper function than just say write your own helper function would be a valuable addition to the language. I want to ask two questions with regard to one of your statements about the problems with the helper function. Regarding static analysis. There were two static analysis arranged. The first is TypeScript point of view and keep it specific to TypeScript but probably applies to other TypeScript like systems if we’re concerned about those. 

TypeScript has a never return type, and it’s in statement position when you call a function that has a never return type, TypeScript seems to understand the implications of that well in expression position for like a nested expression after an or. We know that TypeScript doesn’t infer as much about a never return type as
we would like it to. I believe it is filed a bug on TypeScript about that or found there was already a bug filed. I don’t remember which one. So I want to verify that a never return type on a helper function solves the TypeScript Problem. That’s the first of my questions about the static analysis.

RBN: It does not currently. So the reason why TypeScript – let me back up for just a moment.
If you have a throw statement or you have an expression that returns never and you invoke that
at a statement level, we treat that as program termination. So if you were to declare a variable before that, not reference it before the expression that returns never and then do an assignment to it afterwards or try to use it afterwards for some reason, that would as part of the control flow analysis be considered a variable that has not yet been referenced and affects certain other behaviours that fall out of that.
So if you report on errors related to unused locals, if you want to use, do type analysis or control flow analysis to determine what is the type of this thing after I have gone through an if statement and then or else branches might throw, only at the statement level we check that. We don’t currently check that at the expression level because it’s too expensive. It is too costly for us to descend through the entire expression tree and do a control flow also through the node to see if something is program terminating and instead we used things fall out of saying the never expression is returned and if you try to do something on it you
get an error on the subsequent line. It won’t be unused levels and we don’t do the tracking on that level because of the cost. The error.throw would only work at the statement position and in this case might just use a throw statement. Doesn’t help us in an expression case.

MM: What I don’t understand about that answer is that in order to make the adjustments to TypeScript so that you get the TypeScript benefit from the ThrowExpression syntax, whatever those adjustments that you make the TypeScript would have the expense that you’re currently trying to avoid –

RBN: No, they would not.
I’m sorry for interrupting.
Please continue.

MM: No, no.
If that’s wrong, then that’s the high priority to focus on.

RBN: The difference is we can perform static analysis and analysis of the code based on syntax and know it's a ThrowExpression with a throw keyword and keep track of the AST as we do with many other things.
We know as we parse the tree, that is what is going to happen at that point so we know we can dig into that specific part of the expression to know the things after it that follow are, might have the no unused local errors, et cetera. For any given call expression, that is literally everything.
That is every single function you call anywhere. If you do that for every single expression it would increase the compile time drastically. Now we’re having to do these other checks we don’t normally do for expressions everywhere and too expensive for that to work. Syntax is extremely beneficial for TypeScript in this case because we don’t require type analysis, we only can require analysis of the syntax that is a much smaller area to look at.

MM: Okay.
I’m glad I asked.
That answer is a surprise to me and new information.

SYG: This is one of the reasons why I reached out to SYG prior to the presentation to kind of get some idea if there would be any benefit to escape analysis. Currently there isn’t.
As I understand it, the escape analysis that it uses is fairly coarse grained. I see there is a potential future for that. And other mechanism for static analysis that I think would benefit from the syntax that you really can’t get from the expression case, with the method case.

MM: So I think that these static – and my second question was going to be about the what the
optimization opportunity for the engine, so that was relevant. So I’m open to doing a deeper investigation into the difficulties with the helper function. I’m not willing to write those problems off as impossible to solve in TypeScript and clearly the engine thing since it’s not an opportunity that can yet be realized at least in V8 what do we take to realize it and whether it can also be realized for helper function given the kinds of Git optimization that we have that are conditional on very static methods being the original
static method like the math functions. So I would be open to doing a deeper investigation into both of those static issues to see what the genuine costs are. Until I’m convinced that those costs are unsolvable with the helper function, I’m unwilling to give up on the helper function.

RBN: I think there’s a clarifying question and there’s one other topic I wanted to discuss in relation to this.

GCL: I just wanted to ask a question about the previous discussion there. Were you saying that it’s more difficult for TypeScript to understand the error helper function than it is for any other random function that returns a bottom type?

RBN: Not more than – not that it is more difficult for error.throw but that it is difficult in general for any function that returns never.

GCL: Okay, thank you.

RBN: And to a point that I made earlier that we couldn’t just look at error.throw and give it special handling because if anyone says const equals error.throw we can’t handle that case. The moment that you step off of the path you have issues with it not doing things that you expect.
We don’t want to break user expectations in those cases. So I wanted to go before we continue with the queue, I wanted to go briefly to another point around syntax related to Mark’s question.

I have and this has been something that I brought to committee and I have considered in the past a proposal to allow you to have an expression-less throw that could be used inside of an catch clause, especially a catch clause that has no binding, where I don’t necessarily need the binding because I’m not going to use the expression, I’m going to do some type of work related to an exception being thrown and just rethrow. There are a number of languages that have that capability.

That’s not something we could do in the expression case if we use a method because that would require the method having some understanding of whatever the current ambient exception is when I’m being called and so I don’t think that’s viable in my – well, again, this hasn’t been presented. It’s something that I might bring to committee in the future.The only way that that might eventually work and still be supported in both the statement of expression cases that you have a syntactic equivalent. I think we can continue with SYG.

SYG: So I want to – a bunch of the conversation just happened around speculative benefits to Gites, I don’t think there’s much. I’m kind of confused why this is touted as a big benefit now. For throws, it makes the rest of the code dead, there’s some pretty simple unreachable thing that happens for throw statements since their statements that clearly makes the rest of the thing dead.
For expressions, that might help there but I’m not sure what benefit that is. I guess who is writing code where the rest of the body is obviously dead in expression? An expression context. Like, I’m not sure that would help any performance. And I think I just didn’t understand what the escape analysis question was that you were asking me, Ron. 
What was it that

RBN: I was trying to better understand if there are specific optimizations that happen in V8 related to if you were to early return or early throw and aware that V8 has escape analysis of object construction and object properties if the values don’t escape the method or function, they could be converted into local variables, but then I guess in my question was, are there constraints around when it can do this such as branching? If I throw in one branch, if I throw something that might use the value in one branch but not
another or if I can – are there any type of optimizations how that happens? Does it have it in an option in the branch where it needs it as an object or try to maintain it as just local variables for those cases and if that is the case for something like throw and return as a statement, is that something that could conceivably be applied to throw in an expression case to assist with the escape analysis in a branch?
That is what that question was related to. In general I think the syntax is more interesting for static analysis cases even above and beyond escape analysis. If that’s something that V8 has optimizations for throw statements already.

SYG: At the point where escape analysis is usually applied in JSVMs is pretty late or pretty high tier   optimization that would apply and escape analysis is expensive and not do it in earlier with byte code analysis or compilation. By the time you get to the higher tier JIT you have intermediate representation than a syntax obviously and this could be something – V8 has something like sea of nodes that JVM compilers use and I think it’s more widespread to use an SSA like form but at that point, you have some 
IR, and once you have the IR which is likely to be post in-lining in the higher tiered JITs, whether you have the helper function with the ThrowStatement or ThrowExpression, I think the difference between those is basically nothing unless like you’re helping function is really not in lineable for whatever reason. If it is a built in helper, then that gets straight forwardly in line as like a node in our IR that says throw and there’s no difference between the statement and expression there. If it is a user written helper, that is not used in some weirdly wild metamorphic way chances are it can get inlined and it will be a tiny helper and that ThrowStatement will be a throw node in the IR of the function it was inlined into and I don’t think it will be a big difference or any difference between the statement and the expression. So in terms of enabling more optimizations, I don’t think this would enable any more than what would otherwise be possible today via a user written helper. As for what optimizations can utilize, you know, try to leverage dead branches for narrowing or something and I think that’s orthogonal question and I don’t see how this would really make a
difference there.

RBN: Thank you.

NRO: This is regarding the topic from earlier when like Kevin said that actually we could have
an expression of throw and you mentioned WH objected to that. I was checking the notes and WH was actually pushing for a solution with the lower precedence at some point and all of his concerns that are captured in the notes are about like how syntax and make it look like some sort of weird precedence for the expression. Like, I want to not speak for him, so I’m trying to – I have notes in front of me.
He explicitly said the solution using the grammar and not having verse or restrictions is his
preferred solution. And there was the discussion about syntax or function and syntax we could still consider having an expression of a throw, regardless of whether we go with option 1 or 2 and in the future we could still like once this gets used we can see if we can relax parentheses and how much is in
practice.

RBN: I wanted to point out – not point out, rather, I would say I attempted to have this discussion with   Kevin prior to plenary.
When I checked my chat history in matrix I have a bunch of things that say that the message was
unable to be sent.
But I was – I have actually been investigating a way of doing this without the look ahead restriction. Potentially feasible. So it’s something that I need to talk more with the editors in which case again this becomes if we went with the approach it’s more editorial discussion if we write it. I know it would be more complex how the grammar is specified because it requires using something akin to the in parameter production where we allow it in some cases and not and some things related to how we do things like multiplicative expression – right associative that would actually be able to solve this without the look ahead restriction. There is a potential for that to work in that vain where it’s just how it falls out in the grammar.
So WH did say that his preference would be throw UnaryExpression and like just working out something in the grammar, but we did also discuss the specific alternative of having parentheses throw and in that case didn’t want to have the right-hand to be UnaryExpression. That is sort of the intersection of all of the proposals is parent thesed expression of UnaryExpression and that can be relaxed to the proposed alternatives and we discussed the specific alternative and WH specifically did not like it for reasons that are not totally clear to me but I think that would be a weird thing to do, not on any practical concerns.

RBN: This is the sliding question from the September plenary where we basically were proposing so mething akin to what we’re talking about today but maintaining UnaryExpression precedence. And I believe his response at the time was that this results in a confusing precedence where we parenthesizing and still only allowing Unary and he suggested we pick a precedence and our position at the time was that we essentially had picked the precedence. The precedence generally preferred by the champion and generally preferred also by Kevin despite his objection around the restriction, the restriction here is UnaryExpression is the right precedence and the most convenient for users and fits the majority use case but we would have to find if we wanted to meet that have to find an alternative syntax that would leverage just the grammar as it is. That’s something that I need to continue pursuing if that’s the direction we want to keep looking at.

NRO: Thank you both for clarifying.

RBN: So the thing that I would like to get to before I close is that again we have a choice. We can look and the option 1 or 2 choice which is always using parentheses. This is something that I’m not particularly fond of. I think this is detriment to the users and continue to parse this as UnaryExpression with maybe not a look ahead restriction but something that preserves the same goals and we also have the discussion or the topic raised by Mark which is to do a more detailed investigation into the effects of this just being a static method on error, for example. And while I still have concerns around the static method approach, it would affect type languages and potential expense of being able to have the types of benefits. I think also talenters would have issues with that and linter would essentially require full type checking to be able to tell you if you have unused variables because you failed to – you threw something beforehand. I think there are some concerns there and something we can continue to investigate. So I’d like to get an idea if we have a specific direction to work towards from the syntax side while we continue to investigate the expression side or the method side. Since nobody is adding themselves to the queue the champion preference of the syntax side is continue looking down option 3 with an alternative grammar that does not require look ahead but has essentially the same effect and unless there is an objection to that, that pushes us on to a path of option 1 or 2. Nothing on the queue?

USA: I would take that as consensus.

KG: I don’t think you necessarily need to rule out using lookaheads in option 3.
I think we should consider the grammar that we want and what is the editorial simplest way to get it; that might not be lookaheads but it might be lookaheads. I think that the important thing is we figure out which programs we want to be legal and which programs we don’t want legal and then it is a strictly editorial concern how we write that down.

RBN: I agree with that. So in summary we’ll continue to investigate the syntax options with the editors to find something that we think will generally be palpable and try to continue along the UnaryExpression path if we we find this is viable alternative or option and we will also be investigating the static method case and look for some feedback on to the possible caveats with that and I would appreciate if implementers can reach out to me after the meeting and I regularly speak with Shu and can talk to him about this as well and if anyone else can reach out to me afterwards to get an idea – so I can get an idea are there concerns they would have or benefits to syntax versus expression in those engines, that I won’t necessarily have direct visibility to myself.

RBN: I have it on here potential for seeking advancement and only if we had a definitive conclusion
as a result of this.
I did also catch this as potentially just an update because there are some depending on whether we went with any of these options may have been open questions and I’m happy to just leave this as an update for now as we continue to pursue these options. Nothing else, I think I am done, thank you.


### Speaker's Summary of Key Points
* List
* of 
* things

### Conclusion
* List
* of 
* things


## Incubation call chartering
Presenter: Shu-yu Guo (SYG)

- [proposal]()
- [slides]()


USA: Is somebody ready to do incubation call chart, Shu, do you have any pointers as to who took
over from you?

SYG: I do not.

USA: It seems like we don’t have anybody to take this idea on.

SYG: It’s been ad hoc for what it is worth.
Since I’ve stepped away due to lack of cycles, I think there have been a few like about decimal and maybe something elsewhere it’s just people coordinate on the GitHub issue if given that it's been fewer proposals  wanting to do that, with having just a few like zero to two between meetings I think coordinating on GitHub might work out well instead of this formal thing and shelf this formal chartering for now.

USA: Sounds about right, but just in case I could do a quick call again: Are there any proposal champions on this call who would like to have an incubation call or during this meeting that mention the possibility of having an incubation call later?

[silence]

## Function and Object Literal Element Decorators for stage 1
Presenter: Firstname Lastname (RBN)

- [proposal]()
- [slides]()

RBN: Good afternoon. So I am Ron Buckton from Microsoft corporation. I wanted to speak to you for the next hour about extending the decorators proposal further than we currently have.
So a brief overview of how this presentation is laid out: I am going to talk about the motivations and then there are two parts to the proposal I want to discuss. Function decorators, and then kind of a more specific focus on object literal element decorators and then discuss the broad scope of the proposal towards the end.

RBN: The main goal here is to kind of look at the things that are not currently decorable, but are – have similarities to what you can do with classes, class fields and methods. So that’s why these are grouped together as opposed to two separate proposals. There’s a lot of cross-cutting concerns across these that are worth addressing together. The main motivations for this is function and object literal element [dem] rarities, would allow the meta programming capabilities  beyond classes and class elements. Those capabilities that we now provide with or will be soon providing with decorators are the ability to attach or create reusable blocks providing logging, facing and entwined things for HTTP or REST API routing. Authorizes flows, paired with things like AsyncContext in a multiuser service.

RBN: The ability to perform registration of classes such as HTML custom elements. The recent addition of metadata reaching stage 3. The subscription or recording of metadata of objects that can later be used.
And some other additional things interesting like the ability to write generator trampolines, that the generator mechanisms.

RBN: Also, we want to really be able to promote decorator reuse regardless of your programming style. Decorators today as they are tailored towards class, class methods are focussed on OOB based development. Where the object oriented style. But kind of leave in – leave in the lunch things like functional programming, folks who attend to use object literals versus classes for instance values. And there’s some benefit in having a shared and reusable mechanism for defining how these decorators apply. Because if we are able to define a decorator that can be used on class method and on a function in a way that is consistent, it simplifies how we do validation for decorator placement. Where can he get this used or, test where whether the thing that the decorator itself is decorating is valid? We get API consistency for decorator – with the decorator context, allowing us to have essentially simple switches at the top of a decorator determine, am I targeting the right element? And generally improvements that we get with a context over just a DIY unary function pipelining

RBN: So with those in mind, again this first section is about the function decorators. Here, decorators we already know are useful for class methods, they’re not convenient wrapping mechanisms for methods and class syntax. A field, a function, you could pipe through a function, using a call back like approach or any other type of function wrapping, but that doesn’t work for overriding base class methods and defer actual logic to the base class definition. It doesn’t work well with field-like definitions in those cases. So we have this decorator syntax for class methods to give us the metaprogramming compatibility that aren’t convenient to do when you’re talking about an object literal or a function. And for regular functions we can and do write things like what is picked here: call function, function with the thing you want to wrap. And that’s kind of like a decorator. It can do something similar. Why would we want or need the decorators? There’s a number of reasons that we want to consider this. One is decorator reusability. We can write decorators that are designed to work with a class method, a class static method, designed to work with a class constructor. That could also be reused for functions. Things like logging and tracing. If I have @log decorator I could apply a method, I could apply that to a function, and have that same benefit. The API consistency is key. Unlike the regular function pipelining approach, I don’t have to – I can make a determination as to the thing I am decorating whether that is a valid decorated thing by looking at it and accessing the kind property. That makes the programming model for decorates applying to – function declarations in FunctionExpressions consistent with methods today. And how those checks were-pd and allows us to look at other things we might want to decorate in the future, and if those things were not to have – not to also have some type of differentiating context then how do we determine the thing I called with as a function is not actually a decorator? Today we do that by validating the arguments provided to us.

RBN: One of other things you don’t get with pipelining functions to be function wrapping is, this post-wrap registration compatibility that you can get today with `context.addInitializer.`  It’s a condition text used for custom elements where you can define the class context; other decorators that run, might do a constructor replacement. And at the end of the day, what is the final result is what is passed to the add extra initializer, to those call backs, allowing you to do this post-hoc registration of classes. You won’t get something that is partially ready because there are other deck rates to be replied after it.

RBN: Some other things we get as a benefit that we don’t get with the function wrapping is reflection. We get access to lexical assigned names after declaration via context.name. If you are some a number of levels deep with multiple decorators, and one of them doesn't forward the function name, but you want the logging purposes, the pipeline functioning approach that you could do today is not really sufficient for that. It requires quite a bit of trust or work on other function decorators, and ensuring you get the right name for this.

RBN: Another – compatibility we provide here is the ability to define metadata. You can attach metadata to the class constructor using `Symbol.metadata`, we could extend the same capability to functions because a regular JavaScript function can also serve as a class. You can construct it and have the capabilities. Also paves for future extensibility for other proposals such as parameter decorators. I had brought parameter decorators, which is currently at Stage 1. I brought it to the committee before this, because there is a specific need among the typed script community as now that we have the decorators proposal for classes, class and class elements at stage 3, we can start looking at ways that are types – projects using typed script legacy experimental decorators transition to the native support. But one of the things that is valuable in the TypeScript legacy decorator case is the ability to use parameter decorators for a number of reasons which are described more in detail in the parameter decorators proposal.

RBN: Function decorators are something I have for a long time considered a separate proposal. It was originally discussed as part of the original decorators proposal when it was first introduced. And the only reason why parameter decorators came first is there is an apparent and immediate need on the TypeScript side to determine whether or not those are something that is viable, something we have the compatibility to do. Function decorators are something we don’t support in TypeScript, not because we did not think it was a valuable direction, but by the time we were implementing this, the idea of function decorators had already been kind of pulled out of what we were considering for this proposal.

RBN: So this gives us a way to provide the future extensibility. What would it look like? A couple of examples here on the screen. You might have an event handler, you want to add debounce semantics to a method or a function, in this case, than arrow functionment I might asynchronously retry in the error during IO operations. I might have an authorization mechanism in a multiuser application in something like node JS, where I want to have multiple incoming callers access information at the same time and be able to thread through context right armedless as to whether I pass it as an argument. I could use this for that
And the idea of being able to use a generator tramp [poe] lean that does something like data flow, that you might hold in the body of a generator function and then have that be presented as just a regular function to the person evoking it.

RBN: Now there are some other interesting use cases. So one that I was pointed to, to look into was, AWS – this is to simplify the creation of services in AWS. And [chal]ice uses python. It is in the Python language, which is the class decorator we have today that was very heavily influenced by the decorator in PSI. And PSI does have these abilities to decorate at regular functions.

RBN: And this here is an example of the same example you can find on the README for AWS Chalice into JavaScript syntax, using a JavaScript function decorator. So you have the same capabilities: defining REST APIs, scheduling tasks that are picked up by the service, responding to events. So all these things that don’t require an entire class construct to be able to leverage this compatibility.

RBN: Another capability this has is the capability to write function decorators that decorate decorators which sounds redundant, but the ability to define mechanisms to reduce boiler plate. I mentioned before that one of the things that is valuable with the decorator syntax and context is the ability to validate the thing that you’re decorating is correct. We have `context.kind` for that. That’s a boilerplate to add to every function. In a language like C#, the attributes have an attribute you can put on an attribute, that allows you to specify what the valid targets are for the attribute. Those are processed at compile time versus what we support in JavaScript would have to be validated at runtime. This gives you the same capability. I can have a decorator and decorate it with additional context that can simplify the process of defining these and easier to see at a glance by putting in documentation, you document those are to see them and know what effects they will have. Same thing happens here with the allowed decorator, on another decorator that says, I am allowed to be placed on methods and functions and then reduce and remove that boiler plate that you have to otherwise provide. And have the other additional context. Here, you can see that I can infer from the context of the decorator that this is targeting the name of the function that I am decorating, which is useful for producing a TypeError that can be caught and reported upon and gives you more insight into what the method or function or whatever it was that you were decorating, whether that is valid. This is a function inside of the method without having to be repeatable and rewrite the name yourself or using stacked traces to try to figure it out. This is a more reliable and clear mechanism for it. And give the types of tests. So you can see with this example here, if I tried to decorate a function with this decorator, it’s okay because it would be allowed to decorate a class with this, it’s not okay and throw an error. It gives us those capabilities.

RBN: So, how this works, we expect or would anticipate that decorator evaluation order and application order are the same as what specified for class elements. Decorator evaluation is when we evaluate the expressions that are part after the decorator. So if you see `@foo`, it’s the `foo` part. Whatever that expression is, is evaluated at that time. In order, in document order, essentially left to right, top to bottom. As we expect. Decorator application is the reverse. It preserves the application order it sees for this function application mechanism so we apply them right to left, like you have nested them. Evaluation order and application order differ. We want to make them consistent throughout the language. This is similar to how methods or classes work. You replace the thing with something that wraps it using a function. Return and defining it has no change; anything else is an error.
And much like with class decorators we could have extra initializers after they are applied and get the final functions for things like registration. And we again would have the ability to define metadata on the function itself.

RBN: So a lot of what is here, it might be more than what you normally see for a Stage 1 proposal because it goes into the API shape, but this is all designed to – borrows and base from the existing syntax and existing API, the goal to show we are intended if this is adopted to align completely with how decorators are defined today. In that case, just like with any other decorator, the function get the target you’re decorating and the context, which is a kind that we switch on, the name, the object that can be used to attach metadata and the post-wrap, post-decorators initializers like registration.

RBN: And as we said before, just like normal decorator, you can return something that IsCallable, that is the wrapper or the function replacing it or undefined which means no change.
So for most functions, the FunctionExpressions and error functions, this is fairly simple. It’s just taking what we already can do and have already specified for decorators and applying it to these types of functions.
And for the FunctionExpression cases, this is fairly simple: there’s not anything beyond that that we have to consider to be all terribly complicated, but there is a problem with function declarations. And this is something that is a little bit difficult because of hoisting semantics.

RBN: Now this is something we have discussed in previous plenary sessions outside of plenary as well, since 2016. When decorators were advanced first, first proposed and Stage 1, we have had numerous discussions about this and a lot of time was spent on trying to figure out does this mean introducing a pre-evaluation step? What would pre-evaluation step mean? How does that affect imports and exports and there’s complexity here that needs to be further explored that we really made progress on since the idea of function decorators was passed on for the MVP portion of the proposal
There are issues with a pre-evaluation step that we have investigated.

RBN: Decorators functions, we have suddenly become observably initialized one at a time for others – this isn’t the case for functions today. It makes it problematic. For a pre-evaluation work, decorators wouldn't local variables, they have to hoist to the top of the block that might have a const declaration that is a constant variable, that we might want to reference from a decorator. This works fine for a class decorator, or a field one, fine for even function expression, but for a hoisted function declaration, that variable reference would be in TDZ and not apparently why. We changed the order of the evaluation from the user’s perspective. Is this workable? I don’t really think it is. But I think there are other alternatives. And that’s one of the things we want to explore further in Stage 1. And hoisting is a thing we talked about in the past. I do have another topic on the agenda to further explore this. In some of the direction, further exploration and getting committee feedback as well, that follows this topic.

RBN: If you do have topics specifically about function hoisting, I would – unless they are specifically point of order or clarifications, I would advise holding those until we get to that later discussion, which I intend only to have if we decide to advance to Stage 1.

RBN: So as I said, there are some options that we have been considering. Again, introducing a pre-evaluation step is one. Another which is something that I initially experienced with, way back before deck decker rarities were proposed, it was based on one that I had written and handed off, and myself and Eric, worked together on the original decorators proposal. But my earliest implementation as I was fleshing out the design tried to do something that you – dynamically on command. There are problems. But it is something to consider.

RBN: The third bullet I have here is an option which is that you don’t hoist decorated functions. This is the champion’s preferred preference. There’s a reason for that, it fixes a lot of issues. That decorates on functions might have a one-time cost of possibly having to move that code. But there is – that cost of having to move mode is potentially there even if you were to do a pre-validation step due to decorator order observable. So it’s a cost you might have to pay, even if we allowed hoisting. In general, and the conversations I have had over several years now in this proposal – as I have been working putting this together, the best solution is not to hoist. But again we will go more on that, shortly. Also,  we could decide to not allow decorators on function declarations. We can make those metaprogram capabilities reasonable across the language.

RBN: We really don’t think that’s a viable solution. It may be the one we have to pick, if that’s the case. But I think as we go through more investigation into this, it’s – I think it’s a fairly easy thing to rule out. Another thing I have considered over the years is a opt-in marker. I can get to more of that later. My preference is do not hoist. And what means is that non-decorated functions continue to do the thing they have always done, hoisted. Decorating function declaration would basically end that value hoisting behavior. Still hoisting the variable declaration like we do with any function or var. The actual value that that is initialized to is not yet defined. That would mean that code authorization might need to reorder the code, and again that could be something that you actually need to do anyways, even if we did hoisting because of the observability of evaluation in those cases. That also means that this is something that you – you’re paying the cost for it, when you use it, you don’t have to pay the cost for existing code. You don’t have to pay the cost for – somewhere else in the code, that just happens to be in the same lexical scope and that causes other confusing things to happen that aren’t expected. We think this is the clearest and most reliable mechanism. You don’t use second-guess evaluation order and it means all the decorator applications would align with other usages. This in general, the champion, hopes to take this approach. Everything is under discussion as we continue to do this investigation.

RBN: So we do believe this hoist issue can be addressed, and we believe it’s warranted. This is something we have discussed, on and off over the years since the initial proposal for decorators that’s not resolved and it’s worth getting into, finding out is this feasible? Something we can do? I would like to have a longer, more focussed discussion on the hoisting issues that’s been scheduled later after this topic. So we can kind of get some perspectives and hear some of the concerns that folks have or might have raised in the past, or raised off-line. So we have a direction to go if and when this advances to Stage 1. Given that and couching this in the exact mechanics of hoisting are something I want to get into later. I would be perfectly happy to take discussion topics to the function decorators part of this now is if anyone has anything they want to add to the queue.

DE: There’s a – there’s a lot of discussion on matrix about the fundamental motivation for this proposal. And I think we should probably discuss that before getting to the hoisting discussion. In my opinion, this is a lot clear and very different from how a functional application feels. And will encourage the construction and use of libraries that do these sorts of, you know, function application over – over function declarations. Anybody else want to talk about this? 

KG: Yes. I would like to hear more about the motivation. The slides were not - it did not feel like there was motivation other than "I want a new way of calling functions" and I don’t think we need to have a new way of calling functions that only takes functions arguments. And most of the examples on the slides, like I was doing the rewrite in my head to what that looks like if you do it as existing JavaScript, and it's almost indistinguishable. Maybe I am missing something, but . . . 

RBN: Again, one of the – the motivations for why decorators in general apply to functions as much as they apply to classes, class methods and fields. The same capabilities and expressivity you want to have there are the same types of expressivities to have for functions. As to specifically if we already have this for classes, and class constructors and methods, why do we extend to have it on functions? And a lot of that is described in the slide. The specific motivations that are on top of the existing decorator motivations are primarily around consistency. You could take a decorator that you would write specifically to use on a function, and write one that is specifically – specifically used on method and write a version that is specifically for use on a function, that works conveniently. But if I wanted to take a decorator, that I write to be used on methodthat has – a I want to reuse the compatibility on a function, it’s not so simple. It’s not just I am going to call it with a function. It’s now, the person who wrote the decorator now has to be defensive around am I being beaten with one argument or 2. If there are other things we do in the future, we also would need some type of additional context to know, is the one argument call for a function or for something else. We don’t know yet.

RBN: So trying to write a decorator that is defensive to be called in both cases is not easy to do and it’s easy to forget how things work, easy to piece the pieces. Having a consistent API design is very efficient and helpful for end-users writing them. There are things that you cannot easily do with function pipelining that registration case, which is heavily used in the examples I showed relating to AWS Chalice. These are function registration. They do something after the fact and they are simple cases that show one decorator. You could have multiple. If you have a decorator that does registration that has to be held at the end of a decorator call, you have to be careful about how you document it and explanatory about what this means and have to be concerned about if someone later on comes and adds another decorating, not looking at the documentation, but for the thing decorator they are applying, so there’s a convenience with the function call – function lending approach, `F o G o H` of the thing I am doing as a the function to make sure I don’t put the G in the wrong place. It’s one of the values of add initialization that you get with decorators you don’t get with regular function calls. The other thing too is that – 

KG: Hang on. You have listed three things already. I am not going to be able to remember all of them  and my responses to all of them. So maybe we can talk about them one at a time. 

RBN: All right. 

KG: So let’s see. The first thing you said was that this is just a generalization of existing decorators. But that’s not true. There’s no convenient way to do class element decorators or the things they do. Because you can't declare a method that is the result of a function call. But you can have a function, a binding, that holds the result of a function call and that was the only reason I was okay with class element decorators at all. They are adding expressivity and therefore generally useful.

KG: With regards to the registration case, I grant this is something that is not 100% straightforward to do by function application. Because with function application you have to care about the order you’re doing things in and here you don’t. And so maybe that’s the case we think is worth adding new syntax to solve that particular problem. If so, it should be the focus of what we are doing and not like this is an incidental benefit. When you showed the AWS Chalice example, I am not familiar with AWS Chalice and it was not obvious it doing something other than straightforward application. Now, it sounds like the only benefit there is just that if you have a thing which needs to be called last, you have to call it last and document that. And that doesn’t really sound like something that is worth adding new syntax to solve. But maybe that’s something to talk about.

KG: And there was a third one, I have forgotten sorry. I have to check the notes. 

RBN: And again, one of the things that I touched on that you didn’t there was the ease of reuse of existing decorators. As people are writing the decorators, being able to apply them in multiple cases without having to significantly overcomplicate the – the loading behavior. 

KG: That’s right. So the – on that point, there’s two relevant things to say there: the first is that it’s not obvious to me that this is something that really comes up a lot. Like, decorating a class field is a super different operation than decorating a function. And like I would expect to be treated differently most of the time. And it’s not like it’s hard to extract a common thing that can be used in that case, if that’s something that comes up. You also mentioned the possibility of more things to decorate in the future and that this would be consistent with the other things. And granted, in a world in which you can decorate many other things, it could be weird not to decorate functions, but I don’t think that’s a motivation for decorating functions. If we had bunches of other stuff, and then we decorate functions, sure – but that is not a motivation to decorate functions in the absence of decorating a bunch of other things. I am quite skeptical of the other things that we have discussed; of decorating parameters, especially. So I don’t think that can be an argument for doing function decorators. 

RBN: So regarding . . . this is something I hope to address more, when we talk about parameter decorators in the future, but we have experience in TypeScripts with folks that love this feature that make heavy use, large applications use this. So we do have experience with shipping this feature that shows that there is definitely a use case for it, a need for it and it has a very valid place.
And this proposal itself is going to be in the second half discussing the [voe] – many other things to decorate. And we want – if we do go down this path, and I really do think we should, we should be considering classes and functions only differ one is called . . . well, both are constructible things. The main difference is the body of the syntax in these cases. But there is no reason if I can decorate a class that I could not theoretically apply the same decorator to a function and that is designed to look like a class and the same type as the output. That reuse is extremely important. I think there are broader things to consider. Will is a limit. I don’t think we should decorate variables or any random expression. But I think that many declarative things have value. Specifically functions because we already have multiple types of functions you can decorate, you can decorate a constructor, a method, a setter, we have a lot of the cases in the language, and having this consistency across the things to reuse the functionality, I think is extremely important. Especially logging, tracing, those are things that make no sense to do functions that you can use methods. 

SYG: Okay. So before I say my personal take here, my understanding of the two sides here is that it basically boils down to vibes. So given it is not new expressivity, one side basically likes this kind of declarative metaprogramming and one side does not. Given it might be helpful to do the old day exercise, if people recall, when – what is that thing called? 
Comprehension expressions were killed, the exercise was, a white board, and you would write the comprehension expression on one side, and then the nested for loop on the other side and then that was enough of a visceral thing, to get a more honest, signal from people instead of just thinking in your head, I like decorates type of programming. Or I don’t like it. It might help for Ron or somebody else to provide side by side examples. In this case, for the function case, because it’s not new expressivity, because in the matrix there were cases where someone said, here is how you do it with functions and some people said that’s terrible. Others said that is fine. I don’t know how to weigh that.

SYG: So secondly, I agree with Kevin on the skepticism for parameter decorators. I am not completely convinced they are a good idea. And I would – I am more neutral on function decorators. But even if function decorators were to advance, the last bullet point – I will like there to be some explicit recognition that that does not in any way open the door to parameter decorators. 

RBN: I’m sorry, I am trying to clarify. Are you stating that you would – that this proposal should advance – if this proposal were to advance, it should not care about parameter decorators or something that you would block? 

SYG: More the latter, but I don’t want to use such strong language right now. I am unconvinced we should have parameter deck rates. Parameter decorates, if that changes, that speculative changes how you think, we should talk about it. 

RBN: If possible, I would like to chat with you more about parameter decorators off-line. I will say that the way that TypeScript implements parameter decorators for legacy decorator support, you could declare them on parameters, they were essentially transformed into a function decorator that had additional condition text about the parameter there. If we already have method decorators, they are a convenient syntax for doing more things. I can go into more in depth conversation off-line. 

SYG: Sounds good. 

GCL: So first qualify this by saying, I am speaking purely about function decorators. I don’t really – I am neutral on all the other decorators. But coming from the perspective of other languages, I started working at a company that uses a lot of python a few years ago and was new to the decorator pattern at that point. And since then, I think it’s just like it’s not expressing anything that is impossible, which I think has been a point of contention here. But the way it allows you to structure your code is just, frankly, different from anything you can do. In the language. Not by the literal expressivity of what values you can pass around the VM, but in the – like, how you are expressing your code to humans. And I think that is a very important thing that we should not discount.

GCL: Simply by this argument of the – I don’t know, exact semantics to achieve this behavior, even though it’s completely readably different. 

KG: Just responding somewhat narrowly . . . I think python decorators make sense because it doesn’t have function expressions and if it did, decorators would not make sense in python. My experience of decorators in python is they're not meaningfully different from function calls. They just are functions. And the structure of the code is really not that different, except that you can't have function expressions in python. So decorators are a workaround. They’re not anything other than that. 

GCL: You are correct that they are mechanically function calls. But they are like in this case in front of you reading code, they are different. Different ways of organizing the way you are structuring your code.

RBN: I will also agree that the case of – function method, the way you read and approach them is preferable to the pipelining case because in the pipelining case, especially if you have arguments that you have to supply before or after, or you need to get to the actual function definition, depending on how many levels deep of decoration you’re performing, the pipelining case is only convenient at one level. If I need to apply two decorators or three, the pipelining case quickly becomes much harder to read. The decorating case is much more convenient and I wish I had a slide that illustrated that as well. 
 
KG: Yeah. This is another sort of narrow response. Ron, you mentioned that people have adopted decorators in VSCode or found they liked them. I am not denying that there are people that like decorators. Every feature of every language has proponents. But we should not add features purely on the basis that there exist people who like that feature in other languages. That’s – that would be far too much. So there needs to be something more than that.

RBN: In a way you’re presupposing that parameter decorators a new thing in TypeScript. But they were designed – I don’t know where things ended up when we first proposed it, but this is something proposed for TypeScript – JavaScript not nor TypeScript, but they adopted it because there was a need, we had feedback from folks like the angular team who had an interest in this. So we pushed for that within TypeScript but it was based on a design initially included in the original decorators proposal. This isn’t new. And we have been making changes to kind of unify how decorators work in the TypeScript case, use parameters decorators, many of these people use that in a language that is essentially JavaScript with some extra – with a few extra bells and whistles. But it ends up being an adoption blocker. The folks that use parameter decorators can’t. So ideally, we would like to evolve. It was part of the initial design, but the focus we had was on specific need for classes and parameters, so function decorators got pushed back a bit and by the time the proposal for decorators was getting to Stage 2, function decorators were not part of the design. We wanted to wait until it shook out. Things that are related to decorators, need to hold until they reach Stage 3. We can get implementation experience. A lot of the proposals have been waiting in the wings for five or six years. Till the point we could start getting that broader adoption and bringing these in. Again, these aren’t – this is a feature that another language has that I would like to bring this. This is a feature that is literally designed for JavaScript from the get-go. 

KG: I mean, you have designed it because people like the feature in other languages.
Yes. I recognize that this has been a long project, but I’ve been clear in my opposition, as long as we have been discussing it. Method decorators had a reasonable use case. Parameter decorators I have always opposed. And function decorators, I have been skeptical of. I recognize this is a long discussion, but it’s not been – yes, people use this feature in TS and other languages, but there are lots in other languages that people use and like features of; sometimes, anyway. Decorators especially I would not describe as universally beloved in every language or by every user. So I don’t think that can be a reason on its own to add something to the language. It has to be argued on its own merits. 

RBN: I will say on the function decorators case that even once decorators reached Stage 2 in multiple discussions, that we had, in the various decorators calls over the years as things were getting to the point to get to Stage 3 we had a number of individuals ask about function decoration. The only solution with the proposal was you write as a static method and tear off the method. It’s not really a convenient way to do that. And this is – I have seen this discussed in I matrix and Mathieu’s as well, one of the main goal is improving the ergonomics. I have a use case . . . that ends up unergonomic to do from an I am – implementing those decorators. I am hoping to dig into this more and provide feedback and more of the discussions. 

MAH: Yeah. I am not convinced that decorators would be really – introducing new syntax, because the syntax costs are paid when we add them for classes. I view this as making the language more consistent, or authors being able to use the decorators they use on a class method, and being able to use it on an object literal function.
This is actually a use case that I believe – we would have to be able to similarly apply the same decorator on an ObjectLiteral function, ObjectLiteral the same way you use it on method.

MAH: If you can apply it there, why not on freestanding functions as well? 
I think in my opinion, this is [inaudible] exploration for Stage 1. As extending the already existing decorators to other places where authors feel natural to use them.

LCA: Yeah. I also don’t want to – like have a specific preference here for parameter decorators or not, but I totally agree with Mathieu that like Kevin’s point about we don’t need to add any feature that anyone likes in any language, I totally agree with this. We already have the syntax, for classes and methods, it only makes sense we add the syntax to functions. It is – like this comes up that people ask us, why can’t I decorate a class, a method, a function? And RBN has shown that there’s real use cases to do this. There is affordability, use cases of – yeah. Decorator reusability. And like if we were in a situation where we had to decide whether to do decorators at all or not, I think Kevin your point makes sense. Yes, we don’t need to add every feature. But we already have this feature. It’s a matter of extending the feature to functions, to make it consistent across different pieces of the language. 

KG: I really don’t think that class element decorators are that similar to function declarations. You are not – class element decorators can affect placement and the relationship of the thing to the class. They are not just a transformation.

LCA: Yeah. But they can also just log out whenever the function is called. Or do things like that. Right? And these are case that are totally valid on regular functions. And people do this with class methods. And it would be really nice if they can use the same – the same bit of code to add tracing or to add logging or whatever to class methods, but if they could use that for functions. Like there’s – yeah. This is not any more complex for users to read, they know about it because of classes. 

KG: Other than the hoisting thing. 

RBN: To respect the remaining timebox and I don’t have much time left. I have more slides to get to. I want to object to literal syntax and come back to this more.
Since I think the object literal syntax also has some bearing on this discussion.

RBN: I don’t have that many slides for object literals. So we can come back to some of the topics in a moment. The other half of the proposal was to talk about the other things that are also things to decorate. Again this is all things I would like to explore in a Stage 1 proposal. So having the same types of benefit that for class field decorators and getters, et cetera, this would be nice to extend to object literals. We got requests over the years, why don’t we support object-methods? This was considered in and out of the proposal at various times for the normal decorators as well.

RBN: We want to investigate supporting decorators on object literal methods, including getters and setters. Property assignments and shorthand property assignments they have – they are essentially the same as a singleton instance class field. They can be evaluated in a similar way to have some of the same benefits there. Possible investigating using accessors avoid the – anyone doing any type of like – define property shenanigans to turn a property into a getter setter instead of the same level of syntactic transformation. Not on the object literal itself, with the exception of fields. Decorators today generally apply to funky things. The accessors are added because there’s a get set. But producing a pair of functions and fields are still function like because even though it’s not effectively refiled, the function initialize – other, the field initializer is treated as a function in the specification as far as span semantics and everything else works. We think there’s a value there. But not in the object literal itself. We think that might be a step too far. Getting closer to the put a decorator in any value and I think that’s too far afield. If we allowed this, we would allow it to spread too. There’s other things that happen there and it’s not – wanting to bring in something else’s property, to not mutate that thing. That’s another area to look at. This is roadway iterating before evaluation and application order. That we want these things to be consistent across the language. Wherever they are used. So methods getters and setters would behave the same as class methods do. Property assignments would behave the way class fields do. We reuse the same capabilities on a property assignment. Allow accessor, the same thing for accessor fields and classes and the same return semantics in those cases. Extra initializers are run after the decorators are applied, and than you can reuse the same functionality  to run on whatever the final – what the object literal looks like at the end. And get their own metadata referenced as necessary. These are all things to investigate the viability of. And as we talked about with functions, they would have again the same type of shapes so we use the same programming model around how we write decorators in those cases. There are a couple of questions we want to investigate here: like do we need to indicate specific differences between object-methods and gets versus regular methods and getters. [sop] other distinction? Metadata, where does it get placed for object literals versus where it’s placed for classes? 
Things like private and static make any sense or if they do how and that relates to the overlap of methods, getters and fields and how these work. Those are things we want to address there.

RBN: And since we are almost out of time, I will skip the object literals-specific discussion and go back to a general purpose discussion here to say, we are looking for adoption of Stage 1 to continue to explore this. I am hoping that at Stage 1 we will have the opportunity to have more of these in-depth discussions about viability of the proposal, about the specific areas we think have some contention and need consensus and resolution on and have a chance to really dig in and explore of the features with anyone who has concerns or specific needs to have addressed. I am hoping we get Stage 1 to keep the investigation going. I will open up to the queue. I don’t know if it’s possible to request a short extension for the topics remaining. 

MAH: Yeah. So  I am actually interested in object literal methods and being able to decorate them. One thing is not possible with – even if you had a property that had a literal function, if you were trying to wrap using a function wrapper, you couldn’t get the property at that point. So with decorators, from what I understand, get it the context of did it name. There are a lot of things for object literal, it seems deck rares would be more similar to classes there and that’s what I am looking for. 

SYG:So, I said this in Matrix as well, just for the record, RBN  mentioned angular in passing for some of the use cases. And talking with the Angular folks internally at Google, what I have heard is that there are at least Some contingent of angular people that can sit at the current state of affairs


SYG:With its decorators to be full of foot guns and unwieldy, and they have regrets there. They told me the at inject decorator is on the way out. And I’m not aware that there are plans for angular to move
Off of it’s dependence of nonstandard decorators. And I say that as, as – as a point that Angular is not likely to be a motivating customer. Not saying there are not others, but I don’t think angular is one in this case.

RBN: I would like to talk about that a little bit, I mentioned Angular. I was not talking to them specifically as a motivator, but they were the initial motivation for the original decorators proposal and what we discussed. We had numerous discussions about angular decorators over the years. The one distinction I recall was that the big push for decorators both on the typeScript side and being proposed in plenary was around something that allowed you to do met programming, and allow you to have runtime impact on behavior. At the time angular primary focus was on annotations, just things that would effect compile time behavior. And I believe most of angular current use of decorators is something get transported away at compile time. They don’t generally have runtime dependence on runtime decorators I do agree that angular is not a particular motivating case right now, but I also think the way that angular chose to use decorators within their framework and applied it really didn’t speak to the benefits of decorators to begin with. And like, the idea around Metadata and how that works was initially informed by some of those early
requests for how do we do things like attach annotations when they didn’t want to have or didn’t, their use cases weren’t concerned with full metaprogramming capabilities. But full metaprogramming capabilities is where we are with decorators are powerful and flexible. Without angular as a motivating use case,
There are a significant number of existing use cases in the ecosystem today that are using TypeScript decorators or using Babel decorators and using decorators or transformed with ESbuild and other, and other compilers and generators and, and whatnot, and found them extremely useful. So regardless of the Angular Case, I think there is more than enough motivating cases in the long-term.

KG: Yeah. As you can probably infer from my reasons for not liking function decorators, I’m much happier about object literal element decorators because they do in fact add considerable expressivity. So I’m fine with going forward to stage one for the proposal as a whole. I’m much less skeptical of this part of it. Since it's not just function application.

RBN: I do think it would be unfortunately, even if we don’t get parameter decorators, which I still hope we do it, it would be unfortunately to decorate every other function except a function, I think it would be a huge inconsistency in the language in the long-term and even if we don’t get object literal decorators, it’s still that inconsistency that I think is unfortunate that I would love for us to eventually be able to address in some way.
 
KG: I just don’t see that you are decorating functions. You are decorating class elements or object elements, and in addition classes, but that is primarily to interact with class element decorators. The proper understanding of most decorators is that they are of class and object elements, not of functions. Most of the kinds of decorators are of class fields or with this proposal it would be object fields. It’s not of
functions. It is not that it is like this weird kind of function application that only applies when the thing you are applying it to is a function; that would be super weird. It is elements that you are decorating.

RBN: I would argue that I have seen many decorator implementations other classes that just use functioning. They are working with them as if they are Functioning. And callable decorator could theoretically produce a class that works like built in contractors like arrow, you can call it get the right thing. 
People are doing this, it does exist.

KG: Granted that people are doing this. I’m just making the narrow point about consistency. The consistency that would be present, is that you can decorate class and object Elements. That's a coherent story to tell.

RBN: I also think that you may have the wrong take regarding class Decorators, only really having value for interacting with class element Decorators. Things like custom elements and how Lit uses decorators are Related to class decorators themselves, not class element decorators. There are many use cases for class decorators that have nothing to do with the Class elements themselves. All right. So it looks like the queue is empty. Again, this is for stage one. I’m trying to get further discussion and trying to get the ball rolling on this proposal and what we can moving forward to and Get consensus for the future and further exploration of this space. So with that said, I’m looking for consensus on adoption of stage one.

CDA: We have supporters for stage one from LCA and from DE. I also support Stage one. Do we have any other voices of explicit support? Plus one From MAH. Do we have any objections to stage one?

MF: Okay. If this goes to stage one, given the discussion we’ve just had it seems like the exploration is not on literally these particular solutions to this problem. Is it maybe more appropriate to formulate your problem statement a little bit differently and possibly rename the proposal based on that? So that you can possibly address these concerns in other ways like some of the many solutions we’ve discussed on the matrix throughout this –

RBN: Well, I haven’t been able to follow Matrix. I want to have a chance to look at that. I will look at that, but I still would like to couch this as like my main interest here is around the Decorator meta programmatic space and the capabilities that this provides. I have considered potentially splitting function and object literal element decorators into two separate proposals, but really I think they need to be discussed. We already have a decorator propose. For classes and class elements. We have a proposal for parameter decorators. But I would like to be able to discuss moose other kind of core decoratable things in the context of each other. Because I think it is important to be able to talk about object literal methods versus
decorating a function expression that is attached to a property assignment and what the differences are. Are of those things are part and parcel to the same Package. So without having any context of what is going on in Matrix right now. I feel this is essentially the right scope for this. Because I think that this
Is something that, if it is going to advance. It’s likely to advance together. I know parameter decorator have other concerns that are different from these here, that make them better off as a separate proposal. But that proposal, I expect if this advances, would increase its scope to cover parameters on
Function decorators. Right now the parameters decorator proposal is very specifically scoped the class constructers and class elements. Those are the only places you can have decorator today. In the March
plenary, I brought it forward and reached stage one, one of the field back, I believe from Kevin, there is no way parameters decorators could be advance if there were not function decorators. Therefore, there’s a little bit of a dependency order there, but I also don’t think that they are specifically in lock stem with each other. Because we could possibly have a future where we only – potentially have a future where we have   parameter decorators and not this or function decorators and not this. I do believe this is the appropriate
Context. Because we’re talking about, I want to introduce net new syntax and new capabilities to your decoration or decorator like for the functions, I don’t think this is in line with the proposal. The proposal is aiming for a broad consistent mechanism for programming across all functionality things basically expanding what we already have. I’m worried about reframing it that would push scope and direction that is not consistent with the rest of the language. I am not sure I’m comfortable changing it. I will sail the URL at the end of my slide says function decorators. But it is not, I will change the name of that before this would be adopted into TC39 so it matches.

MF: Can I suggest an alternative formulation possibly?

RBN: That’s fine.

MF: It seems like I see two spaces here. One is pretty much what you just summarized here with exploring the rest of the applicability of decorators to these other constructs in the language. But the other seems to be trying to address just the ergonomics around like modifying the behavior of functions. And that, that second part that I mentioned is the one we have been having a lot of discussion on the matrix. So would you maybe be interested in exploring those two separately and having them maybe be dependent on each other or exclusive of each other?

RBN: I’m sorry. Which two parts separately?

MF: Exploring the space of applicability of decorators to other constructs, which I think you summarized just before I spoke. And the space of the ergonomics around modifying function behavior or augmenting functions.

RBN: I’m not sure if I’m willing to, I would be interested in trying to split the proposal at this point in that way, because I still don’t feel like I have enough context what is going on in the matrix. But if we need to make changes to this as part of this further discussion at stage one, I’m not opposed. I'll say also that we have had many discussions in plenary and out of plenary over the years about the applicability of decorators to other things. My understanding and a conversation of others and my own hard line in the sand, as far as I know, what is currently proposed here in the parameters decorator proposal and in the existing class elements proposal is essentially it. I don’t see a value in decorating variables original imports and exports, at least not yet. And many of the cases are things I had gone into presentations and plenary of things that were completely nonviable. I’m not sure a broader discussions on all of the things we can decorate, it might be too broad because we already have ruled out many of those cases and these, basically what we have at stage one and what is I’m proposing here is the net sum of the things that we have considered.
 
DE: I agree that this is probably the set of next things we should consider. Since there was the question of how should we define the scope or the problem statement, I think we could consider this as extensions to decorators. I think it would be confusing or counterproductive to make like a number of other proposals that we’re kind of juggling. We have parameters here. We have like function modifying there. We are just thinking about what should we do next in Decorators. Where decorators are about, you know, modifying a particular construct. And a way to do that. So we could call that decorators V2. Or Decorator’s extensions and within that, you know, we heard strong objections to, you know, parameter decorators. So that’s, like, a piece of input. Considering parameters decorator out of scope, we would be concluding no rather than
maintaining a proposal at stage one or something. And – yeah. I think it is definitely worth investigating what should be decoratable next.


RBN: I’m a little bit concerned about saying decorator V2. Because it provide as very broad scope. I think having more focused scope is more likely to succeed. Big, big broad proposals have that, had this problem about getting weighed down by all of the differences of opinion across all of the different capabilities. And having more focused proposals that pay attention to crosscutting concerns I think are more likely to actually succeed. So we’ll be considering, so as I think, Kevin as on here, he wants them to be explicitly out of scope. And functional decorators are not part of this proposal. I’m not planning to bring them into this proposal. Having both of the proposal as stage one allow us to discuss the crosscutting concerns, if the concerns are met, we are advancing proposals that are consistent. We don’t push something one way that completely prevents something another way, even if we were be able to make convincing arguments for the other’s case. I think it is important for us to achieve the level of consistency across the proposals and again not get bogged down not taking too much. Which is why I was considering breaking this one up into two separate proposals.

KG: Yeah, the things RBN said sound good to me. I’m fine with this going forward as function and object literal element decorators for stage one. And then exploring – it’s not a commitment to that being the precise scope at stage one certainly. As I have expressed, my personal preference would be during stage one we narrow the down to object literal Elements, but certainly I’m not asking for that to be done now. And I think that it does form a sort of coherent body of work with just the things Ron has
presented today. So I’m happy for that to go to stage one. As a bit offeedback for going to stage two, I would like to see more discussion of the motivation; and in particular, I think it would be very helpful to compare what one writes with decorators to what one writes without decorators. There are a lot of people transforming functions today. Someone in Matrix mentioned that they have a web component framework that does registration despite not having decorators and it's been fine. But maybe not is not true for other frameworks. So more discussion of the before and after and how it is better, since it is being primarily proposed as an ergonomics feature. Again that can happen within stage one.

RBN: I can reach out, there’s a number of groups that I think use thisFunction application mechanism toed that, that already use this today in shipping code that would be able to provide feedback on their preference around Decorators and how they would find them useful. I will reach out to some of the
Groups. Material UI is one of them, react has things they can do them in their Code base, but cannot use  decorators for higher function components. A lot of Cases where it looks like people really want to use it, but haven’t been able to, I can reach out and get more feedback there as well.

RBN: I would like to add one more thing to Michel’s point. I think this is the right scope for the proposal, but as we discuss stage one, if we need to broaden or decrease the proposal or split the proposal before this can ever get to stage two, that could be a perfect time to discuss the scope, right now, my primarily focus is on the things presented here. I’m hoping that is enough to at least get us to stage one and then, again, have these discussions as we continue along the process.

CDA: Okay. It seems like your call for consensus was ages ago. So I’m going to ask everyone again for explicit support to advance stage one. We have a thumbs up from LCA. I also explicitly support stage one from this. Do we have any other voices? Do we have any objections to advancing to stage one? Do we
have any dissenting views that anyone would like to record?
Hearing nothing, seeing nothing, Ron, you have stage one.

RBN: Thank you.
     [ Applause ]

CDA: Which I believe means we get to segue into your next topic.

RBN: Yeah, let me bring that proposal up or the presentation up in just a moment. I’m not sure in the time box, how much time do we have left on that?

CDA: We can accommodate your 30 minutes. Yeah. Don’t worry.

#### Summary
- KG believes that decorators should focus on cases where they actually create new capabilities that wouldn’t be syntactically possible otherwise, the way that object field decorators are. He pointed out that function decorators are generally equivalent to a function call, meaning that they don’t add new capabilities. Asked for more motivation for function decorators, especially before/after demonstrations of improved ergonomics.
- DE, GCL, LCO expressed support for function decorators due to mental model, ergonomics and analogy to class decorators
- Future work on decorators should include more example cases for using decorators, and comparing to the baseline case, possible today, of function calls.

#### Conclusion/Resolution

Function and object property decorators reaches Stage 1
## Decorated Function Declarations and Hoisting
Presenter: Ron Buckton (RBN)

- [slides](https://onedrive.live.com/view.aspx?resid=934F1675ED4C1638%21299455&authkey=!AHrmKC0xH_s815Q)


RBN: All right. So let me start this up. All right. I asked people to hold off with object hosting. This is likely a topic with mixed opinions on, and I wanted to get more feedback and need to start collecting that feedback. We discussed this multiple times in plenary over the years it is worthwhile for us to bring all of the people together to discuss this complexly. I will say that we’ve already been discussing various opinions on the viable of function Decorators in general. And I’d like to couch this in keeping those discussions, we can have the discussions offline on the issue tracker and try to keep this more focused on the hosting scenarios that I’m, that we need, would need to resolve if this were to move forward. So this is originally pulled from the function and object limit are decorator presentation when I first posted it on the agenda. 
I made minor changes just to move a lot of content here to make sure this could be more focused about the specific need. As I mentioned before, se talked about evaluation order. Right now decorator evaluation order is left-to-right, top to bottom. Document order. That’s important to be able to preserve the user’s expectation around when things happen, if they capture a temporary variable in a decorator that uses the parenthesized syntax and do certain things or evaluate in a decorator that has a side effect that effect as later, a later computer property name or vice-versa, that is also important to be able to preserve that evaluation order. And application order occurs on decorators right-to-left, preserving function application, like G here. And it happens on, in a specific order currently based on the order which those declarations are defined on a class as part of the class definition evaluations. So that class order is disconnected from the order, but evaluation order is really important to discuss in this case. We talked about arrow functions and function expressions have semantics, because classes are evaluated in the code. They don’t have any value hosting semantics like function declarations do, but Function declarations do have this    interesting capability that they had for a while it doesn’t matter where you write them within the body, within the body or block, they get moved to the top of that block essentially. you can call functions that haven’t actually been reach to point of being declared or might return before you get to the declarations. Those things are still successful. And introducing decorators does complicate that. There are various options we are considering. The five we talked about in the previous presentation I can kind of go into a little bit more detail here and then start taking some questions and getting some feedback. So one option is if you introduce a preevaluation Step. This is something that, I think, has been discussed at lengths in prior plenary sessions many years ago, probably the thing we are spent the most time on for function declarations and no progress has been made. there is talk about introducing a step, and deal with circularities in the input graph, how does that work with decorated functions and then there’s other issues
So make it so this doesn’t really work. At least not anyway that seems consistent. One thing that is decorator, if you decorate a function declaration it seems hoisted, the variable is, but you’re moving execution to the top. You’re not moving just the value initialization to the top. You’re moving actual executionables. But decorators function that run actual code. And things you would have normally expected to work because of hosting, such as if I want F to be able to reference itself, I can do this with a function, I have a function in the body references F. I can even take a reference to this here, because it actually doesn’t have a value yet. That value is dependent on the execution of decorators. And the same vein, this function F can’t use the G decorator, because the G decorator is itself decorator, and the
G function is decorated and hoisted and doesn’t have a value. So hosting decorated functions doesn’t work if you’re trying to work around that case.
 
RBN: So that’s a problem. Another problem I mentioned is that decorators would be able to reference local variables. Even in class decorators this is a very common thing. You have, you will have decorators that have like specific, specific values that they support. Those might be declared as constants, Numeric value that are new raimented object literal. And in this case, I have part of an A Mi8 I want to reuse across multiple decorators. This is something you can do in collapses, but not with function. Decorators, because they would be hoisted above the base and in TDZ. They would not work and be able to maintain evaluation and declaration decorators that you would use on function decorators. You withed not be able to say cage decorator equals arrow function, or await import and use a conditional thing from another module. None of those things would work from functional decorators, that means hosting does not work in this case. And the other thing that function hosting hosting is complicated by decorators is how you expect a evaluation to work. Introducing a valuation, and hoisted functions are run before decorators on other code, that breaks the evaluation order expectations that users already have or would already have for decorators, that they are evaluated Top-down, left-to-right, in document order. That has consequences on ordering Side effects. Etc. So we generally, we discussed this before, that we generally do not recommend this approach. If we move forward with this, because it doesn’t work the way you would think. We find there are too many issues make that is nonviable. 

RBN: That said, we do think there are other alternatives to consider. One is some type of dynamic mechanism. Where function decorators get run when the declaration gets hit as if the execution program had actually reached that point in the code and then it is evaluated like it would have classes. But for things that are not yet available, that’s they would suddenly pick up and do their thing. We first looked at this in function decorators. In TypeScript back in 2016. But that was harder to read over the other 
Sematics. So you have code that never gets called or reached by statement Execution. So what happens with registration, when do the things get called? It is too complicated. Finally, deterministic, you cannot be sure the order your code will run in. And if we did an appropriate that is similar to that, that would require a suboptimal first use for all references. I might take a reference to it and pass it to something, that’s just probably completely impossible, or the performance would be so terrible because it would have to
Be applied to every variable reference, everywhere in every one’s code, and no one wants that.

RBN: So I left often discussing first use checks. This is definitely something we don’t want. We had similar discussions in the past. We generally consider dynamic applications of these things to preserve a level of hoisting to be problematic and not a recommended approach. So the approach that I have
recommended and been discussing with others over the years and even recently, as recently as I think as in Matrix on the suspect plenary, or the march plenary, talking about where the, what we can do about hosting to find something that works. And in the champion’s opinion, the most reliable approach to this, we just don’t hoist decorated functions. There is like class declarations aren’t hoisted. Or at least their values are not hoisted, decorators require evaluation, because your attaching syntax to them that requires evaluation and therefore they can no longer be hoisted. Hopefully, we team this is an acceptable cames because this is new syntax. And new syntax means new semantics and there is no cost paid for using code bases and the cost with the dynamic execution case. The readers have no reason to guess evaluation orders because they would follow the collapse element decoration order. You don’t have to second guess evaluation order. What you do have to do is reorder your code before definition, but you probably have to do that in any of these scenarios. What this does give us we definitely align with other decorator usages if we move forward with this, this is the approach that champion strongly recommends, it is definitely preferred in this case. 

RBN: Another option, you cannot decorate function declarations. You can decorate methods. You
can theoretically decorate object literal methods and maybe even function expressions, but not function
declarations and the question is why the that break in consistency would be a word in the language that would be very unfortunate and doesn’t meet the goal for decorator reuse that being a major motivate recall for this, and being able to leverage the decorators to apply to multiple function-like things without having to have very specific cases for those. So – not decorating functions when you cannot reuse those decorators for function decorations. Maybe that is not so bad. You have to use a function decorator and use it that way, but I don’t think the meets the goals for Reusable for this proposal, it is not an approach we would generally recommend either. One other approach that I kind of shopped around a little bit and
considered is that we kind of mix the no-hoisted and no-decorators approach. 
That is to have an an opt-in keyword, so eventually saying `let function f` is roughly transposed `let f =`
the function and carries over the assigned name from the variable declaration like you would have function
express, but more of a convenient way to label a function. So you have to opt into it, by opting into it, more so than adding the decorator, you have to understand evaluation order. It has the same benefits of the option three approach which is not hoist decorated functions. But there’s maybe a little bit of value it in beyond that and you can just use a let function or a cont function to declare functions that have the same let and const semantics. I don’t – saw mantics, I don’t know if that is enough to visit the marker, but
that is another option over the years. 

RBN: That gets to the discussion kind of things. That in, with the focus on if we do eventually get consensus of advancement of function decorators beyond stage one, what are kind of the opinions and concerns that people have with the various choices? Is there agreement with champion recommendation
or options we haven’t considered. I would like to keep these a little bit focused what we talked about
here. Here are ideas and then go into a little bit more in-depth on the issue tracker as we

DE: I’m happy with this do not hoist decorator functions, I have to admit when we reviewed this internally within Bloomberg, multiple people found the nonhoisted version unfortunate -- the lack of capability to hoist this is is feedback, some of us felt that there is a technically correct answer, but that is not universal. It is unclear how to proceed.

RBN: A lot of people I talked to about this, their first reaction, I don’t want to break hosting. It is usually kind of showing the problems that I described in the earlier slide that it really comes, it really becomes clear that it, if you decorated, you can’t hoist. It just does not work. There are just too many things
that break expectations, evaluations and expectations and whatnot. And most of the people, everyone that I talked to that has kind of brought up, that is there a way to make hosting work or just with the way we understand things as they are right now. 
They come to the conclusion, this approach doesn’t work. I’m willing to open to hear if there are other ways of working this. But from my understanding, we spent two to three years between various plenary sessions with updates in history to update this, and never came to a solution to make hoisting it wo

GCL: Yeah, overall plays one to option three. I think, mostly in agreement with DE here, probably a little bit less concerned about the loss of hoisting. But yeah, I think as long as people have to modify their code to add decorators, that is the appropriate point at which they will need to do other things and I’m comfortable with that trade-off.

RBN: Yeah, I think I heard from the others in the past, many people generally think it is a bad idea to rely on hosting anyways. Anything that uses method. So decorating those are kickier, because we have to move things around. Bought that is a cost you end up having to pay, it is just much clearer with, with this third option, that, that cost has to be paid and you should be paying it, then the does with one where
things seem like they are working and suddenly stop working. I think it is more consiste

CDA: Nothing else on the queue.

RBN: I actually expected a lot more commentary on just the hoisting scenario, I know it was a contentious topic in the past. I’m not 100% ready yet to specifically request consensus for option three, there are other things we need to figure out with the proposal. But I think option three is the most likely to move forward with researching and looking into more of this topic. I can maybe ask for consensus, but I’m going to couch that in, I’m not expecting consensus or specifically need it at this point. But it would be helpful as we make these types of determinations. 
So, if I can be more specific, I would like to ask if, if we do end up moving forward with function decorators, we would consider consensus with going with option three, if not, we will consider this is something that needs to be more broadly researched.

DE: As much as I personally would be happy with settling on option three, it feels somehow a little early to conclude on this. Both because we haven’t gotten further in our investigation into motivation and whether we want to do this at all. And also because we – I don’t know, we haven’t come to a real reckoning with the feelings of missing hoisting would be bad. So we – unfortunately, I think, have to take seriously the possibility that no hoisting is fatal to the proposal. I don’t think it should be considered that. But somehow, we have to evaluate this. So I think it is just too early to, you know, call for committee consensus one way or the other on the conclusion.

RBN: I think that is a completely fair position. It is one that I expected. The main reason I even entertained the possible tale is that there has been a history of having this discussion in the past, I knew some folks on the committee would bring that to this discussion and might have an impact there. But again, I’m not, I wasn’t expecting consensus. I’m perfectly fine moving forward without it. But this gets
the topic on the table and this is something to consider, and I do need additional feedback on and can provide more of the context to the research that has gone into this as well. I’m happy to do this offline as well.

DE: This is a very good analysis. I hope this is a step forward. I hope we can develop this shared understanding that basically we won’t be able to get hoisted decorated functions and we have to decide whether we can live with that. So I’m glad that you brought this so we could – so people can think about it.

RBN: Thank you. I’ll give a moment for the rest of the queue. But I don’t have more beyond this.

CDA: Looks like no additional discussion from the room. Or online.

RBN: Well, I appreciate the committee’s feedback. And I encourage those interested to reach out once the proposal repo is moved on to have more in-depth discussion on the various options and as we go along we can come to a more detailed conclusion as to the result. But again, thank you for your time.

CDA: Did you want to dictate any key points or summary or conclusion for the notes?

### Speaker's Summary of Key Points

RBN: The main key points or summary, I think on this one, I have introduced five alternatives we have currently been considering to address issues with hoisting decorated function declarations. We don’t have a specific consensus discussion as it may be too early on this. And champions current preference is to not hoist decorated functions. But it needs further discussion.


## Approval of ES2024 and opt-out period
Presenter: Jordan Harband (JHD)

JHD: Hi. So, in order to have the office specification every year and follow all of the appropriate steps, there is a 60-day opt out period that, it is a royalty free patent opt out period is what it is called that nobody has ever exercised in the past, but we need to provide to make sure that if any company has contributed something that they need to be removed from the spec, they have to opportunity to do so. That period must be completed before the General Assembly meeting which is typically in June. But also our second meeting of the year is typically in March, this year it is in April. That’s a tight timeline. So on behalf of the editors we’re going to ask plenary for official approval of the specification, which would be whatever is merged in main plus the normative, there are three possible, or three normative changes that would be added in addition to that, one is a ray buffer transfer, one of them is NRO’s normative PR about host, or sorry, is about HTML comments. And another one is PFC compile strings normative PR, open in November and received consensus in a previous plenary. The three normative changes are open and expected to be reviewed and merged in the ES2024 specification produced within the next week. So that, that and at that time, I wid file an issue on the reflector as I have done in previous years to notify everyone that the spec is available. I will also create a GitHub release on the repo if you are perhaps watching releases on the spec repo. So that’s the ask for the room. Also plenary approval for that.

MLS: Is it my proper recollection or am I getting old that this is a vote by one vote per member? I know in the past we have done it by acclimation, but I’m just, you know, wondering if that’s how it is done. I can’t remember – but I seem to have some recollection.

DE: We have done it both ways. Both ways are valid within the process, my understanding, acclamation is kind of easier. So. So –

JHD: I guess the first ask, can we do it by acclamation?

DE: Yes, yes.

JHD: Can we do it by acclamation?

CDA: Yes.

JHD: So, not hearing any objections to approval by acclamation, we can officially record that ES2024 candidate draft is approved and that the draft will be posted on the reflector in the next week. Thank you.

[ Applause ]

MLS: Will, will that also start the opt out period?

JHD: Yeah, the intention is for the opt out period to begin the moment the specification is created and posted on the reflector, approved in advance today.

MLS: Okay. Thank you.

[Break]

RPR: Okay. Let’s begin then. I think we’re back with RBN. And before we start that, it’s – you have a clarifying question about ES2024. 

IS: Yes. So actually, I didn’t understand it. So “ES2024”, which standard is it? “ECMA-262” or “ECMA-262 + ECMA402”. That’s all.

JHD: That’s a good question. I was asking about 262, but today, 402 editors should ask the same questions. So perhaps they can signal the chairs when they are ready to do that.

RPR: SFC, maybe that’s something you can do later? Please keep that in mind and we can return back to it.

RPR Okay. Thank you, IS.
## "Discard" (void) Bindings for stage 1
Presenter: Ron Buckton (RBN)

- [proposal](https://github.com/rbuckton/proposal-void-binding)
- [slides](https://onedrive.live.com/view.aspx?resid=934F1675ED4C1638%21299430&authkey=!AC1WJedcJV5utmI)

RBN: I get to wrap the 100th TC39 meeting.I am here to present this session is this concept of discard bindings. And I say void here, I will get into that in a moment.

RBN: So the concept after discard is a non-named placeholder for some time of variable to variable binding. It lets you elide a variable in a number of different contexts. This initially, came up in the using declarations proposal, one of the core capabilities in the original proposal that was introduced way back when, for using and try-with-resources, a variant and try-using and then using-value and then using-void. The idea was the ability to have a using or a wait using declaration where what is important is the site efficiency by reusing existing resources allocated and want to adopt it into a scope – block scope with the appropriate cleanup semantics. Or declaring a variable resource that you don’t actually need the reference to. This is very common in the case of something like a lock in the shared structs multithreading world or feasible to do that with SharedArrayBuffers. Or even async coordination and concurrency without parallelism. It’s a thing heavily used by tracksal support, if I am doing transactions and commit the transaction when the thing is complete. If I don’t need to hold on to the variable reference, you will end up with a dangling reference that is unused and to have a specific handler in linters to say, we don’t care about that for wait and await or name things that we don’t need. And other places where this could be useful.

RBN: When taking the using declarations proposal and trying to scope it down to a MVP proposal of, to reach Stage 3, I wanted to come back to was void bindings. It’s something that is extremely value for the proposal. I have had multiple sources providing feedback that when they want to do it has this compatibility where they don’t need to declare the binding and there isn’t a good solution for this. One of the reasons we pulled this out of the proposal is there’s actually a broader set of places where this could be useful beyond just using declarations. We intended to bring this back as a stand alone proposal.

RBN: Some of the places we discussed, and considering, are function and method parameters, object and array patterns, extractor patterns and one of the very important use cases is pattern matching as discard patterns.

RBN: Right now, we are proposing the use of the void key word. In place – as something you would use in place of the BindingIdentifier. We are open to other other suggestions. I don’t have a specific need for it to be void. There are reasons and rationale as to why I picked this key word for use. In this case, I will go into that a bit. The idea is not a new thing in this programming. It’s been around forever. C and C++ allow functions to have unnamed parameters. C# has the ability to introduce discards in pattern matching. And python has this in some spaces. Rust has this and Go. And in all of these cases they use the underscore character.

RBN: There is a little bit of a problem with underscore that we will get into here in a moment. But to really set the motivations for looking for this, there are often needed for declaration side effects without the variable binding. For places that I have to have the variable – a binding to have things handled properly, I don’t need the first parameter of a callback but there has to be something in place. And given need back in committee over the years, anything that could be legal JavaScript identifier is essentially off-limits for these use cases. We can’t use underscore. There is existing code that does that. We have had discussions about the possibility of letting you repeat underscore if you’re not going to use it. There are side effects that could be problematic with that and there is still legacy code  in the wild using lodash and that wouldn’t be consistent with this use.

RBN: Another thing is tooling often with a – and parameters and things where you have to use disable line comments or prefix parameter names with underscores or variable names where things you may not use right now. More of this – this tends to be the case with parameter declarations but things you want to ignore but you have to give it a binding. And there’s been cases of this in the past where we have had this compatibility. We introduced bindingless catch and that was – that practically made it to Stage 3 in one meeting. It didn’t. But to Stage 2 when it was first adopted. Because it was so valuable to people to have a mechanism in declaring this without a user's binding I am not going to use. That is a single purpose. Bindingless works with catch because of how it’s written. We also have elision, which works with arrays and we will get to the ish in a minute. 

RBN: There are alternatives. Could you use an empty object pattern instead? No. Empty object patterns have semantics associated with them. You can use an empty object pattern as a parameter if – to replace a parameter if it might be null or undefined. Using declaration specifically has very specific handling for null and undefined: it doesn’t throw, because it’s not an object with a [goes]d method. And those specific semantics avoid complicated cases using switch and other things or having to duplicate code for resources only conditionally available. So null and undefined are valuables for using, soes not sufficient there. In addition, using doesn’t allow binding patterns. This was a decision during Stage 2. There are differences of opinion as to what a binding pattern actually becomes the resource tracked? Variable you’re assigning – the value you’re reading from to be assigned to the variable or the individual properties? And change there is an – ordering semantics around how resources get tracked that don’t work if I can change the order of the parameters in those cases. And the decision was there to not allow it. Object literal patterns don’t work here. And array patterns are less likely to work because not everything has a Symbol.iterator.

RBN: Using simple elision is not sufficient. If I had _lock = new whatever locking mechanism or primitive, object to create, I can’t just do the underscore lock. = is syntax. We can’t leverage elision. And we can’t use open close paren, like it doesn’t exist, it looks like a CallExpression. We run into cases where we just can’t use the existing mechanisms that we have in ECMAScript to define those. This was originally part of the resource management proposal. This proposal again has had a non-binding form since Stage 1. And it was cut just before we got to – reach Stage 3. And the reason it was cut was not because we didn’t think the feature was viable or important, but because we needed the scope to the proposal that would reach consensus and we needed more investigation into the void because the – the discard portion of this as a broader proposal.

RBN: And as I mentioned before, there are numerous use cases for these bindingless things in using await and using. Scope locking with workers and SharedArrayBuffers, logging, tracing,  and avoiding the prefixes.

RN: And again one of other things that is interesting and useful with discards and used in language, to skip parameters I don’t need. They don’t need a name. One of the hardest problems in programming is naming things. Like calculating the leap years for February 29. Naming things is hard. Especially when you don’t care what the name is. So underscore really isn’t that useful because I can’t use it multiple times, you end up with `_`, `__`, `___`, et cetera. And it just throws and is unreadable and isn’t that useful. So the idea here is that you could replace the binding you use for variable with some type of thing that indicates that there’s nothing there. Again we are using void here. I will get to the rationale for that shortly.

RBN: But this allows you to avoid these types of naming situations, both leading parameters which is useful, but also trailing parameters. There’s an issue with trailing parameters in that in cases like shown in the example, if I am subclassing something had a is expected called with arguments, but don’t use the third argument, I can ignore it and while implementations have improved on how this works, there is still overhead when you're undersupplying an argument list. There's an adapter frame used to adapt what you called it with what the function expects, and call – V8 with I think it was Bloomberg’s help, had reduced the size of the adapter frame through some stack ordering tricks to reduce that cost. But there is a cost associated with that, so there’s a performance gain to be had to be able to explicitly supply the arguments that are supposed to be supplied. There is a side benefit with that as well.

RBN: One thing that is also hard to do well – working with object binding and assignment pat when you have a rest pattern. If you want to pull something out and skip certain properties from that final object, I want to remove them, I have to give them a name if using a binding pattern. So if I wanted to write this using JavaScript today and pull off the Z property and only spread – or the rest of the properties go into the obj2 variable, give it a name like `_Z` or `_` et cetera that I don’t need just to strip the property off and this provides some extremely utility here that I can just name the things I don’t want it it removes them from – from that result.

RBN: Another interesting case here is array destructuring. So this code in this example is maybe a bug, depending on who wrote the code, how well they commented on it, and who reviewed it. I am expecting to exhaust two elements of the rarity, only – expecting to exhaust 3. But because trailing elision in array destructuring runs into a possible collision with – with trailing comma, you have this issue where what you are get here is two elements, even though it looks like it might be 3, because I have 3 possible places in the array. And that’s again we will strip off the trailing comma and the rest is elision. Discard bindings are more plexity to state that is something I am expecting to remove, I don’t have to worry about counting my trailing commas and eyeballing things. This is obvious what you intend to do in this case.

RBN: There is something how assignment patterns, that I think I go into more detail about. What void looks like versus a void expression. But suffice to say, the way this is designed or proposed at the moment is that this is only in the case where you might want to potentially introduce an actual assignment to a declaration.

RBN: The other case that this is useful for is in pattern matching. We would like a mechanism for an irrefutable match. It always matches. Always true no matter what. Every single pattern matching system that I can think of across multiple languages has some mechanism for discard pattern. The idea here is because something always matches, you can use it to test for the shape of a thing without necessarily needing to care about what the value or type of that property is. So this mechanism, you can do things like matching against "is this something that has a Y and X property". Draw a point of that shape. This can’t be done in pattern matching without it. Unless you do something else. I might have to introduce a custom matcher that just returns true and now the overhead of following this in the runtime to interpret its results. So get the Symbol.matcher property. It’s expensive when you don’t care what the result; but want to say always. And there is no really – really no other way to do this pattern matching without some mechanism of disregarding that result.

RBN: Pattern matching also has cases like the terminal case where if nothing matches, what do you do? 
And this is something that requires some discussion in the pattern matching proposal and more focus on that proposal when it comes back to plenary for an update, but one thing that it does currently is uses the default key word, like a case statement, this is what we do last, it’s potentially not necessary, when void. Because that would be the irrefutable match and does the same thing. We don’t necessarily need that case specifically. We might use default for that case. Being able to use void anywhere in a pattern and useful and almost a core principle. It’s something we can’t do without having some syntax. The main discussion we had in pattern matching: if we have this in pattern matching, make sure it’s consistent with destructuring in other places to use this so we use the same syntax.

RBN: Other places we might want to use this have similar needs that we might see with the structures and pattern matching. Extractors, I might not care about the first argument that comes out after an extractor. I need a discard binding. You could use elision and spec does, but this runs into the same issues with elision that the regular rules do, having to count trailing commas depending on how things go with pattern – or with the custom matching result. This is something we need for extractors, but not do that in the proposal, because it’s a broader-reaching area to discuss.

RBN: And there’s the potential for discard in other places. It would be consistent with using and parameters and just have it be consistent, even though it makes no sense to use it. There’s basically a small benefit to it, which is if you have for some reason written code that uses a parenthesized CommaExpression to do some work before you assign something to a variable and cannot split it across multiple lines, then it does make it more readable to do it this way. So just for consistency reasons, I weakly recommend its inclusion, but don’t have a strong preference either way. The main reason to include it would be consistency.

RBN: The last question we are looking into, is whether to have discards in array literals. It is a goal to make elision more explicit. Because while you can write something like void – one comma void comma 2, it’s subtlety different than 1, elision, 2. So that inconsistency or difference is in the example below. Where if you use void 0, you are saying, put the element undefined at this position. If you have an elision, you are saying, do not do nothing at this position. So that actual – value isn't assigned on the array. It doesn’t have a property of that name. This is a very subtle distinction, it’s not one you generally want to rely on. There is possible confusion if you use the void key word for an expression case and it’s not void expression. But maybe there’s some value. This is one I don’t have a real preference on whether it should be included. The one thing I have a preference on is if we do decide to include void in expression positions or not expression, but in array literal position, like we would for a spread element of an array, then it must be consistent with elision. Because that’s the primary goal. I would not generally support void with no expression as meaning the same as void 0 and having different semantics than what the elision would do because you’re not replacing elision in those cases.

RBN: Another place that I have considered but have ultimately recommended exclusion is using discards in catch clauses. We already have bindingless catch. It’s more convenient to elide the binding in catch. You already have to elide the entire parenthesized part of the declaration. And it’s possible as we have been investigating pattern matching and extensions into something like pattern matching in catch, is that we could theoretically leverage void patterns or discard patterns in catch bindings instead if you want to have that explicit declaration or indication.

RBN: So as a result we think the benefits are even considerably weaker than var let and const. We recommend exclusion for catch bindings. The only reason is consistency, it doesn’t have the small benefit that let var and const do.

RBN: Other places we looked at and excluded are class and function names. Versus, let, const or using parameters, you can elide them the syntax that fences the identifier is well recognized, and the existing mechanism is well understood compared to elision that is easily confused. In general we recommend elision for these cases. It’s not valuable.

RBN: Then there’s the possibility for other places. We could do, do we need void fields? That has a meaning we couldn’t override because void is a valid property name. We can’t use them here unless it’s a computed property name. Should we allow it in import bindings? We have valuable forms author that already. Import and then the module path you want to import. Or if importing something from a module, just you do it – don’t include it. There’s no point to do an alias in import. So regardless of the sine would, that is viable, it’s something we don’t generally recommend in this proposal.

RBN: So that’s basically what I am trying to propose. I did mention void -- the reason why we are using the void keyword in the proposal, I meant to circle back to this but jump back to an earlier slide. We can’t use underscore, which is again the de facto standard across every language that almost every language I checked discards. It’s very common to use underscore, but – for reasons discussed in the past and prior plenaries using anything that is an identifier is out. We could use a different hash or anything symbol, but what – they don’t have a semantic meaning to provide. Anything, using at = really doesn’t convey what that means. Using void though, we feel the void does have a semantic meaning that is representable. Because a void expression evaluates the expression and disregard the result. This is to evaluate the expression or initializer and discard the binding. There is semantic meaning maintained between void as a discard versus void as an expression. That is well-maintained with this proposal. But that, like I said, I am open and welcome to looking and exploring other tokens, other punctuators, if necessary, other key words, I don’t think there’s any reserved words in JavaScript that are as semantically similar to discard more than void is. I am using void because that’s what was in the – using declarations proposal, but not tied to that specific name or token going forward.

RBN: So at that point, I will go to the queue. We can discuss what is on there and I can talk about potential advancement. 

NRO: I strongly support this. I was recently trying to use `using` declarations for real. And I find myself writing `using _1 = something`,  `using _2 = something` because I was forced to give a name and I didn’t care about naming them. And there are other cases in which I use something similar. I tend to use just the underscore void, that works well, except when you need it twice. But it’s – like, it’s good to not have like – when I have to use underscore, then I have to add something else in the same scope, I need to go – like, the second one has just to be something different, following a different pattern. Strong support for Stage 1 for this. 

RPR: Thank you. JSC says: “This is interesting. And I support exploring this. End of transmission.”

JRL: Also support. However, I hate to void keyword. It looks too much like a regular parameter name. And one – the underscore works so well, I came from Rust, is because you scan over it. It doesn’t take any weight when you’re looking at it – a list of other things that are actually important. If you go back to any of the slides that have multiple void key words and the things that you want, void looks like everything else that’s in that list. Sorry. The parameters or destructures. It makes it more obvious. Exactly. Void looks like a parameter here. And it’s on syntax highlighting or colouring to figure out it’s not. Underscore didn’t have the issue. It’s absent. It looks insignificant in comparison to everything else around it. If we can find a non-ASCII keyword, or underscore, like, a symbol, or a – one of the operators or anything like that, that is small and insignificant in relation to the rest of the code, I think that would be much better.

RBN: One of the values that I think it has as the key word is essentially the exact opposite of what you said, which is you won’t see it in – most often case you see it in a using declaration, you can have using declarations that spans multiple lines, you can have comma delimited declarations. And having it formatted with the void keyword keeps it balanced with more things that look like identifiers. If you do need to scan, because it is a reserved word in JavaScript, it will show up with syntax highlighting in that respect, and it is possible, something you want to scan over, that people could update a TextMate language file, within the context, the void keyword is coloured as something that is harder to see if you really don’t want to look at it. I am not opposed to looking at other symbols. I have had a hard time finding one that might have better use as something else in the future. So I’ve been weary about taking up that syntax space. Things like tilde don’t work because I have not yet fully abandoned partial application, and tilde is important in partial application as one of the few things I can make infix within a call to give it that indicator that partial application needed early on in its proposal process. Other symbols, most of them just don’t really speak to the semantic meaning of discard. Maybe star is an option. But I am wary about using star for something, where other languages might consider that to be something that is a pointer declaration. Like there’s a lot of contention with the semantic meaning behind a lot of the symbols that could appear in that case that I am wary of running afoul of. That’s why I chose void. It’s a similar semantic meaning to what we are trying to accomplish and doesn’t require someone to like mentally leap over what is the specific sigil in this case mean. But again, I agree we should explore that. 

NRO: I go with JRL, this feels too visible. It should be more visible than the elision. Like one of the strengths is that elision is to see because you need to do the commas. But also, I would very much prefer this to not extract me when I am reading the variables that I have.

JHD: Yeah. Just a comment about the specific example of a tilde. It means something different. `void` is an existing operator that means basically the same thing, void. It’s fine if we want to explore alternatives and non-ASCII. If it’s something that has an existing meaning, that’s probably going to be too confusing to be an option. 

GCL: Yeah. I feel like overall, I feel good about this. One thing I am a little worried about is like there are some places in the language where introducing this makes sense, and there’s some other places in the language where it seems like we were just sprinkling it in there because we can. I would like to be explicit about which things we are trying to at least target at this point before we, you know, just to define the problem space before we start getting more into like any other, you know, stage 2-type concerns. So I think usage in object literals was an example, to me, where we were just putting it there, rather than actually needing it there.

RBN: The object literal example is something that I expressly was interested in providing. It provides a benefit – 

GCL: Not – on the right-hand side. 

RBN: On the right-hand? I don’t know if I had an example on the right-hand side. I had array literals. The array literal example, I think, was one – 

GCL: I believe it was void – it’s not. I’m sorry. It says not. 

RBN: Other than this is already legal syntax and creates a property named void. 

GCL: Yeah. Okay. I did not see the little tiny not proposed text

RBN:I tried to make this more obvious by colouring them in red. But yes, this is not something I think is worth pursuit in this case. It doesn’t make sense for these. 

GCL: Okay. That sounds good then. 

RBN: Static blocks and object literals you can spread or use parenthesized. It’s not something that comes up in those cases. 

NRO: Yes. So this was proposed in the past in matrix. I would say like for it to be discussed generally how we feel about it. We could consider representing a valid identifier for this. Consider using underscore, how it’s used in other languages and advantages, and it already happens to be used like in different projects, to just avoid a value. We determined it only works for a single value. Even if it's an identifier, we could make it still be backwards compatible by relaxing the restriction to declare a variable with the same name and use const. And in this case, where we – when referencing the binding, 

RBN: I know what JHD is going to say. But this is something because this proposal also overlaps with pattern matching, we discussed that there and it’s a direction that no one in the champions' group wants to go with for the reasons discussed before. And having underscore, you can’t reference anything that is outside of that scope if you tried to use that, which is something like lodash and underscore member commonly use as `import _ from` – from I am 0.er star from lodash, et cetera. I will let JHD speak to that, if he wants.

JHD: Yeah. I mean, so there’s a number of problems there. First of all, you can already in array and object destructuring, repeat a binding name. And only the last one will win. I verified with arrays at least. I am assuming it’s the same with objects. And that’s not really – that’s fine. That’s a workaround. I do that. But it’s not clear or readable or ergonomic. It’s a hack. Repurposing an existing valid identifier, or existing valid one, I think in general that’s a bad idea for confusion and learnability of the language and google-ability of features we add. But also, underscore in particular is used probably second only to the dollar sign in terms of like deny I in terms of single letter variables in JavaScript codebases. Underscore if lodash, is underscore. And I have used it for other things. It violates TCP: If you move around code, it would – mean different things, because the underscore takes on a new meaning. So even if were web compatible, that’s aesthetically nice, I think that should be a non-starter. Not a viable option to even consider. 

JHD: All right. Just to be clear, MF has clarified that duplicate bindings are an error in strict mode. My test was in sloppy mode. Yeah. 

RPR: Yeah. You are next with seems like it only makes sense. 

JHD: So yeah. My next topic is, in general, I feel like this proposal only makes sense in places that are conceptually a comma separated list. The only places -- off the top of my head, where that bucket might not – be something to apply is in array and object literals as opposed to destructuring patterns. The literals creating a value, being referred to as the right-hand side. Even if it’s not always in that exact position. So like I think it’s fine if we like – in object literals, this is clear why it’s not an option. Similarly with class declarations. And in array literals, it seems kind of nice to me that the destructuring and construction of the array could both have the void, but I don’t care that much. And like I still think there’s value even if we exclude it from array literals, as long as array destructuring syntax – that’s a mental model, a simple way to describe it. Places comma separated list except for these two items.

RBN: The mental model is things that can declare something or assign to something. Things that seem a little bit arbitrary like whether you would support it in let const, and var, here, this is still a list. But it follows the guidance things that could declare or assign to a thing so it's maybe not as useful here. Array literals, you are provided a value. So if the rule of thumb is that you could have a binding or assignment pattern, then I think it makes sense.

NRO: Yeah. Once before, like – I want to say that I think we should not have another syntax for array elision because people should not use that. We should not encourage it. 

RBN: And I also agree. I have had this discussion with others. Elision is a bad idea. Because of the conNetflix with trailing comma. So elision when constructing an array literal generally is bad because you end up with a holey array. It’s not a packed array so it’s not quite as efficient and you'll have weird things happen. So in general, I don’t think making this a better – elision better here makes sense. But on the other side where I have to deal with elision because it’s the left-hand side of an assignment or declaration, then I think it makes sense to have something that lets me take the place of elision. Instead of saying, let’s make elision better on this side – for expressions, it's let’s make elision more explicit on the declaration side. I don’t really want this to be part of this proposal. It’s included here so I can specifically state that if we do want this, I think that it must be consistent with how elision works, not with how void 0 works. I am perfectly fine with not continuing with that as part of the proposal. 

RPR: DE is +1. LCA  has the strong +1 about exploring this. I don’t like void. NRO has done the research where underscore is possible and suggests it maybe.

RBN: And just to speak to this again, JHD had responded as to why it’s not viable. I would love to use underscore, but there’s too much prevalence in JavaScript to be usable and someone would inadvertently use the underscore within their code and break other scope in the same scope because it used underscore or lodash. And that means that they can’t use underscore or lodash so change or can’t use the underscore discard. I would avoid anything with an identifier in those cases. I don’t think that’s an option. 

DMM: So in Java, when we introduced the underscore for this purpose, it was done over a period of time. And the compiler started to emit warnings long before the use of it was banned. And then it – then you have a break ask it was introduced with a new meaning and this gave everybody time to switch over. And the two cases are a formalization of how people were sort of using it anyway. I agree, we can’t do it this this JavaScript. We cannot use underscore because it’s too widely deployed. void has a plus point, but it’s at least kind of – it looks identifier-ish. It doesn’t look like an operator or anything, which I think almost all of the other cases won't. If we find an operator that is not got any other valid use in the relevant context, especially I think pattern matching is going to be an important one moving forward, I think something with brevity is useful because as people say, it skips over, even though we can’t make it look like identifier-like in the way underscore does. 

RGN: This is another one of those syntax proposals that is unusual in actually seeming to pay for itself. I appreciate the plethora of examples and strongly support exploring it. 

RBN: Thank you. 

MLS: Maybe I am missing something for right-hand side, non-bindings why is undefined not acceptable? 

RBN: Are you talking about this slide? 

MLS: For this slide, why can’t you replace, you know, the void 0 with undefined? 

RBN: It’s not the same as elision. If you have elision for a reason, where you want the semantics that in the bottom example, one in ar2 is false because it doesn’t create the property because you want a holey array and not packed. That’s what is voice 0 is undefined without using the E word – not key word, using the identifier. It will give it a value at that place and property in that position. 

MLS: Okay. Elision just creates the whole? 

RBN: Yes. It creates a hole. A semantic meaning to what elision in the literal. It differs here. I use void 0 because it uses the void key word.

MLS: Certainly, in function parameters, undefined works fine.

RBN: No. Sloppy mode, undefined is perfectly valid. And it’s one of the reasons why strict mode made that restrict. In the ES3 days, people would declare functions with a parameter named undefined specifically to make sure there was something called undefined because it wasn’t consistently implemented in earlier versions of the language. Undefined I think it was ES3 or 5? It’s a little bit hazy on that one. Undefined was not reliable and there is existing code that is out there that who knows how old it is. We couldn’t change the meaning of that without possibly breaking old code somewhere.

RBN: Also, you couldn’t use it on the left-hand side because again – 

MLS: Yeah. Yeah. Left-hand side, I agree.

RBN: I consider parameter to essentially be a left-hand, the assignment is coming through a declartion like a variable – like a variable declaration would be.

NRO: Yeah. I think you talk past each other. When calling a function, undefined is fine. And not when declaring a function. 

MLS: Yes. That’s what I was talking about. 

NRO: Okay. So this slide is, ible, most of the instruction, and the main goaling of the proposal is for the right-hand side. 

RBN: Yes. This is just an expository. To explain why it’s a bad idea. And just that corner case, if we decided it, there are semantics that I think – that we have to maintain but I don’t believe we should consider this.

DE: Yeah. I agree we shouldn’t consider this. This is a left-hand side feature we are talking about. So in expression context, void is already defined. It’s an operator. In the right-hand side, I agree with you, that if it were to be defined, sure, it might have to be a particular way, but this whole discussion has gone off in a confusing direction. Let’s just focus on the left-hand side destructuring use cases. 

RBN: I am going to change this. It says proposed, but there is no proposed syntax. IT is current ECMAScript. We're spending a lot of time on this slide for something we don’t intend to include it. It’s important it keep a historical record. But this definitely won’t be – I will remove anything related to this in the proposal. 

RPR: And MF, says, +1 for support. Void is a great solution.

RBN: At this point, I would like to propose adoption at Stage 1 and seeking consensus.

RPR: I think we heard quite a lot of support, but please be explicit. NRO is +1. JWK has explicit support as well. RGN has + 1. JHD, +1. Lots of support. Any objections to Stage 1? There have been no objections. So congratulations, RBN. You have Stage 1!

RBN: Thank you very much.
## Approval of ECMA-402 for 2024
Presenter: Ujjwal Sharma (USA)

USA: Thank you, JHD for reminding us that it’s actually time to do this. But yeah. As you might be able to see quickly, there’s very few normative pull requests in ESM 402. As mentioned earlier in the meeting, on the first day, quite a while ago, all things considered. There are no normative issues open right now that are not blocked due to various reasons. So I wouldn’t waste your time going into why they are blocked, each of them. But yet, there’s some major editorial updates that would actually improve substantially the readability of the spec. So you may feel free to go through any of them. This is just one example. And actually, on that note, we really appreciate any reviews. The goal would be to get the signoff of the committee on the current version, the main branch, if you will, of the ECMA402 spec, which is available on the regular URL, and without any normative changes. We plan to go through and merge any editorial changes that began before the freeze, but yeah. That’s it. No – 

(technical difficulties)

USA: Okay. All right. I will just quickly go through them again. While talking about the normative pull requests, I went through this when I mentioned the big editorial pull request. This is the one I was going through, but actually, there is another by RGN. But yeah, like there’s a few editorial updates that we believe make the readability of the spec better, and therefore we try to get in before the freeze. But no normative changes in sight. That’s it. I would like to ask for your approval of the – not this one. Okay. The master branch for ECMA402

RPR: This is only approval on what has already landed? 

USA: Yes, no normative changes. 

RPR: Okay. Do we have approval on calling it for ES2024? Okay. JHD is + 1 for approval speccize thank you, JHD. Any objections? I think we are good. All right. Congratulations. And we are positive + 1 from Dan.