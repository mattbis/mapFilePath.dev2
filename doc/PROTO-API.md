## wip: something like api: right now im adding too much since we have to think about it 

### todo
1. serialiser - is a transformer that applies only to `oq`
2. transformer confusion - is a core include - you can not need any serialiser for example since it could be binary chunks
3. the `bq` | `sq` problem and diff/cache ( massive files, a ton of small files - since peopel have weird stuff ) 
4. the external changes problem. During processing a restat option. On Top level. But with compensator... ( since io )
5. i keep forgetting about subargs and now I just realised `heuristics` is a thing.. I didnt read on it though instead its an after thought until tomorrow.  That though is kinda 7 or 8. since basically its just imperative.
6. api is a bit wordy.,

in this application like before, some args only work around things.. in the main application you can do all manner of madness by overriding the stack but here we must be strict.

This also leads me to think this could have way too many args.. however, its mostly meant to be some kind of API I will use.

- `m`
  - `matts|mstats|mheaders|mcontent|mblock`
  
- `Class Selector` ==>
- `--range`
- `--range -end [topic]`
- `--range -every -entries 512`
- `--range -end -size 1b` the selector means it will only select the first byte of whatever it is

- `q`|`query`
- `q [str]`
  - `qfrag`
- `q [topic] [str]`
- `q * [hash|bits]`
  - `qtopic` ==> ** mod:
    - `qtopic [hash|id]`
  - `qatts`
  - `qstats`
  - `qheader`
  - `qcontent`
  - `qblock`
  - `qrange`

### ports
using engine
  `setVarPolicy`
  `setVarLocation`
  `setLocation`
  `setVarMemoryOnly` ==> `ram://var/**`

`getPort`|`port` 
- `setManifest` -- as below
`_setDefaultFromFilter` create port from a filter type  that searches everthing; you then for example use channels to refine it or whatever... 
`setPortCachePolicy` stop it using shared caches for diff and sync
`setDestructorPolicy` make it not care
`setTransformerPolicy` set what the transformer does
`setRetryPolicy` forces a retry on any channel.. 
`setPolicyLiberalism` changes whether port forces on all channels etc
`setThreadPolicy`
`setExhaustionPolicy` sets how channels merge if exhausted, use their own manifest, a manifest or whatever it merges the current

### all
`setPath`|`path`
`setManifest`|`manifest` sets a whole load of things in one go
`setMode`
`setAttributes`
`setFilterCachePolicy`
`setRetryPolicy`
- SEMAPHORES `start`|`stop`|`pause`
`setPhase`
`getPhase`
`forcePhase`
`exhaust`
`immediate`
`forget`
`order`
`flush`
`_combine`
`_merge`
`_split`

`PORT_MASQUERADE_SERIAL` forces all ports and channels in them to actually be a mono serial machine ( for debugging ) 
`PORT_CHANNEL_DUMP` forces `var/dump` for debugging
`PHASE_PRE`
`PHASE_POST`
`CHANNEL_SIDE` include to allow channels to share data, or set it for the runtime bindings... its faster included. I think. ITs teh default... this is only in a build i guess.. 
`CHANNEL_ISOLATE` dont use any shared caches or structures.
`FILTER_ISOLATE` dont use any shared caches or structures.

### filters can apply to ports and channels...
symbols `filter`|`no-filter`
`setPortFilter`, `setChannelFilter` ==> `setFilter`
`getFilter(cid,pid,fid)`
`setFilterManifest(mid)`
`getFilterFromMode(qmode)`
`getFilterFromAttributes(qatts)`

### channels
`getChannel`|`channel`
`setChannelMode`
`setChannelAttributes`
`setChannelCachePolicy`

### topics
`setTopic`|`topic`
`getTopics`|`TOPICS`
`getTopic`
`_setTopicHash` ==> simple unique hash default
`_setTopichHashType [blake3]`  ==> use blake 3

## Indexing
- `setPortPolicy`::index --> `var/run/p/i/`
- `setChannelPolicy`::index --> `var/run/c/i/`
  - `var/run/s/i/l/`

### processing - applies to a channel or a port ( depending on policy )

`setHeuristicQ`|`heq` the below is a fast average, whilst this is using erm..  here is a guess, its a matrix of averages over time, and this adjusts the queues... whilst the below is set to some finite number its faster, but could go wrong... I suppose there are likely ways to optimise the algorithm used.. 

`setPq`|`pq` you can set it for a channel but in reality its kinda stupid as they exist and go etc
`setHq`|`hq` 
`setOq`|`oq`

### im not sure how people do this I assummed it might be above,,, but this problem could mean `bq` must be enabled.
##### hmm good point... enable this from a mode.. essentially it means a slowdown since we have to find out what is biggest.. or we can find it as we go from what changed, and what we already know...

- for example, a linear single processor could handle this whilst the demand is met for huge files in chunk reads.. 
`setBq`|`bq` 
`setMq`|`mq`
`setSq`|`sq`

`setHqRecord`
`setPqRecord`
`setOqRecord`

`setBqRecord`
`setMqRecord`
`setSqRecord`

`getQMap`
`setQMap`

`forcePqMap`
  - `setPqMap`

`forceOqMap`
  - `setOqMap`

### output
`setTransformer`
`getTransformer`
`getTransformers`
`getOutputPolicy`
`setOutputPolicy`

### runner/runtime/policy
`setScheduler queue[bq|mq|sq|hq|oq|heq]`
`setChannelLimit`
`setChannelPolicy` this means a channel can share results from another channel ( within a port ) 
`setTopicLimit`
`setDiffLimit`
`setTransformerLimit`
`setGarbagePolicy`
`setResident`
`setCachePolicy` overrides or should be first, if the others need some customisation.. or you just use `setManifest` on the port.

### stat
`setVarStat`
`getVarStat`
`setVarGraph(T)`
`getVarGraph(T)`

#### implied, part of it, etc.. or programmable.. 

`Indexer`
`TopicIndexer`
`Stream`
`Writer`
`SlothWriter`


### from prev versions:
`delay`
`lfo`

### for all channels:
`mono`|`serial`|`threaded`
- `setOrder` this can really slow it down
- `PHASE_POST setOrder`


### drafts

The above depends on C, Cpp, Go or Rust since that is the only languages id even attempt this with. Havent decided.

This is intended as just a very fancy way to control how the searching, mapping, indexing, caching, diff works... And is meant for a complex application like the other projects I am thinking about. 

A port is binding to the engine... which is included or a daemon.
A channel is a stream.
A channel is a isolated request from the topic complexity
A channel is a way to request the same topic but in differing live queries.
A topic is a struct, that matches the included build headers... it only matches the default build or a custom one ( since I would not want a giant exe tbh ) - differring via the binding into another language would make it silly.. and this is why this part is likely only a subset at first ( `-images [media-type]` , `-audio [media-type]` )

A topic can be a type of perm query.
A topic can be queried.
A channels topic results can be merged if the channels have the same filter.

A channel will share its physical data.
A channel will share its fs data.
A channel can not share any data.

A filter controls. How channels or ports. respond. Such that a filter can apply across the app or library. Or be used in a channel. The filter effects the pipeline. And cannot be undone. 

A pq will set a type of record that orders the results, and controls what responds first. Or how things are resolved.. in a type of walk manifest.

App can use a new channel to stop the others, merge the results and exhaust. So that you can make it prioritise input from cli. Or leave it to resolve. 

App or library can combine channels or split them for some fragmentation.. 

- traits 
  `[[allow channel to use diff]]`
  `[[allow channel to cache topic]]`

#### todo I am not sure what language and also need to improve the fluent concepts..  and language.
