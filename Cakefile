# Module requires
{exec} = require 'child_process'
sys = require 'sys'
fs = require 'fs'
path = require 'path'
jade = require 'jade'
discount = require 'discount'

# some variables
baseDir = path.dirname(process.path) 
jadeSrcDir = "src/jade"
coffeeSrcDir = "src/coffeescript"
stylusSrcDir = "./src/stylus"
outputDir = "./"
watchers = {}


# Some functions
printOutput = (process) ->
  process.stdout.on 'data', (data) -> sys.print data
  process.stderr.on 'data', (data) -> sys.print data

compileJade = (filePath) ->
  unless filePath?
    jade = exec "jade #{jadeSrcDir} -O #{outputDir}"
  else
    jade = exec "jade #{filePath} -O #{outputDir}"
  
  printOutput(jade)

watchJade = ->
  files = fs.readdirSync jadeSrcDir
  if files.length > 0
    for file in files
      unless file.match(/(.jade)$/) then return
      if watchers[file] then return
      
      watchers[file] = true
      filePath = "#{baseDir}/#{jadeSrcDir}/#{file}"
      
      console.log "Watching: #{filePath}"
      
      unless path.exists "#{baseDir}/#{outputDir}/#{file}"
        console.log "Compiled: #{filePath}"
        compileJade(filePath)
      
      fs.watchFile filePath, {interval: 100}, (curr, prev) ->
        if curr.mtime > prev.mtime 
          console.log "Change detected: #{filePath}"
          compileJade(filePath)
          
          
  else
    console.log "No Jade files found in directory '#{jadeSrcDir}'."

watchJS = ->
  coffee = exec 'coffee -cw -o ./js src/coffeescript'
  printOutput(coffee)

watchCSS = ->
  stylus = exec "stylus --watch --include ./public --use #{__dirname}/node_modules/nib/lib/nib.js --out #{outputDir}/css #{stylusSrcDir}"
  printOutput(stylus)
  
  
# Actual tasks
task 'watch', 'Watches all Coffeescript(JS) and Stylus(CSS) files', ->
  watchJade()
  watchJS()
  watchCSS()

task 'compileJade', 'compile Jade files', ->
  compileJade()
  
task 'watchJade', 'watch Jade files', ->
  watchJade()

task 'watchJS', 'Watches all coffeescript files for changes', ->
  watchJS()
  
task 'watchCSS', 'Watches all CSS files for changes', ->
  watchCSS()
  
task 'compileJS', 'Compiles all Coffeescript files into JS', ->
 coffee = exec "coffee -c -o ./ src/"
 printOutput(coffee)
  
task 'test', 'Runs all tests', ->
  vows = exec 'vows test/*.test.js'
  printOutput(vows)
  
task 'docs', 'Create documentation using Docco', ->
  docco = exec """
    docco src/*.coffee
    docco src/lib/*.coffee
    docco src/test/*.coffee
  """
  printOutput(docco)