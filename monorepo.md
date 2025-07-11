turborepo is a build system orchestrator, not a build system in itself. it directs the actual tools like tsc, vite etc to do their tasks. its initial pitch was to minimize build times by caching build states so new build files do not have to be regenerated again and again if a change hasnt been made to some application, but now for convinience its used as a monorepo framework. under the hood it is still initializing yarn workspace type of thing, not sure which one exactly

![[./attachments/Pasted image 20250331165531.png]]

