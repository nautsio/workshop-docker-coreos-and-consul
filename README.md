# ha-docker-coreos-and-consul
A High Available Docker Platform using CoreOS and Consul

#### Prerequisites:
- Node.js
- grunt / grunt-cli

#### Build and run:
1. From the root install the dependencies with the command:
   ```sh
   $ npm install
   ```

2. Serve the presentation and monitor source files for changes (auto updates, just refresh your browser), from the root run:
   ```sh
   $ grunt serve
   ```

3. Your presentation will be served using localhost:8000. Update your grunt file if you want something else (do not checkin)

#### Structure:
- Except for the title slide, all slides reference external markdown, stored in the markdown directory.
- You can add slides with a sequence of sufficient returns (\n) in each markdown file. Else update the index.html file.
- Images are stored and served from the images directory.

#### Presentation based on reveal.js: more reading:
- [Installation](#installation): Step-by-step instructions for getting reveal.js running on your computer.
- [Changelog](https://github.com/hakimel/reveal.js/releases): Up-to-date version history.
- [Examples](https://github.com/hakimel/reveal.js/wiki/Example-Presentations): Presentations created with reveal.js, add your own!
- [Browser Support](https://github.com/hakimel/reveal.js/wiki/Browser-Support): Explanation of browser support and fallbacks.
