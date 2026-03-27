# table_02_examples


| Natural Language | Generated Command |
|------------------|-------------------|
| "show all running processes" | `ps aux` |
| "find all JavaScript files in src" | `find src -name "*.js"` |
| "list all Docker containers" | `docker ps -a` |
| "show disk usage in human readable format" | `df -h` |
| "kill process on port 3000" | `lsof -ti:3000 \| xargs kill -9` |
| "zip the dist folder into release.zip" | `zip -r release.zip dist/` |
| "show last 50 lines of log file" | `tail -n 50 app.log` |
| "search for TODO in all files" | `grep -r "TODO" .` |
