#!/usr/bin/env node

/**
 * Module dependencies.
 */

var fs = require('fs'),
  program = require('commander'),
  path = require('path'),
  basename = path.basename,
  dirname = path.dirname,
  resolve = path.resolve,
  exists = fs.existsSync || path.existsSync,
  join = path.join,
  mkdirp = require('mkdirp'),
  jade = require('jade');

// jade-web-browser options

var options = {};

// options

program
  .version(require('../package.json').version)
  .usage('[options] [dir|file ...]')
  .option('-o, --out <path>', 'output the compiled html to <path>')
  // TODO implement all the parameters bellow
  .option('-n, --namespace <str>', 'namespace where the templates are placed')
  .option('-p, --path <path>', 'filename used to resolve includes')
  .option('-D, --debug', 'compile with debugging (bigger functions)')
  .option('-P, --pretty', 'compile pretty html output');

// javascript options

program.on('--help', function() {
  console.log('  Examples:');
  console.log('');
  console.log('    # generate javascript from jade');
  console.log('    $ jade-web-assets file.js');
  console.log('');
});

program.parse(process.argv);

// --filename

if (program.path) {
  options.filename = program.path;
}

// --no-debug

options.compileDebug = !!program.debug;

// --pretty

options.pretty = program.pretty;

// left-over args are file paths

var files = program.args;

// compile files

if (files.length) {
  files.forEach(renderFile);

  process.on('exit', function() {
    console.log();
  });
}

var namespace = program.namespace || 'jade';
namespace = 'window.' + namespace;

/**
 * Process the given path, compiling the jade files found.
 * Always walk the subdirectories.
 */
function renderFile(path) {
  var re = /\.jade$/;
  fs.lstat(path, function(err, stat) {
    if (err) throw err;

    // Found jade file
    if (stat.isFile() && re.test(path)) {
      fs.readFile(path, 'utf8', function(err, str) {
        if (err) {
          throw err;
        }

        options.filename = path;
        var output = jade.compileClient(str, options),
          extname = '.js',
          templateName = path.replace(re, ''),
          outpath,
          dir;

        output = output.replace(' template', '');
        output = namespace + ' = ' + namespace + ' || {};' +
          namespace + '[\'' + templateName + '\']=' + output + ';';

        if (program.out) {
          outPath = program.out;
        } else {
          outPath = path.replace(re, extname);
        }

        dir = resolve(dirname(path));

        mkdirp(dir, 0755, function(err) {
          if (err) throw err;
          try {
            fs.writeFile(outPath, output, function(err) {
              if (err) throw err;
              console.log('  \033[90mrendered \033[36m%s\033[0m', path);
            });
          } catch (e) {
            if (options.watch) {
              console.error(e.stack || e.message || e);
            } else {
              throw e;
            }
          }
        });
      });
    // Found directory
    } else if (stat.isDirectory()) {
      fs.readdir(path, function(err, files) {
        if (err) throw err;
        files.map(function(filename) {
          return path + '/' + filename;
        }).forEach(renderFile);
      });
    }
  });
}
