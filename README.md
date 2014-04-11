jade-web-assets
===============

A generator of javascript web-friendly files from jade templates

##Usage:

running the command:
    ./bin/jade-web-assets.js test.jade

generates a javascript with the compiled javascript funcition within a
namesapce

For example, if we have a jade file named tests/test.jade containing the following:

    h1 Hi

Becomes a javascript with this content:

    window.jade = window.jade || {};
    window.jade['tests/test'] = function(locals) {
        var buf = [];
        var jade_mixins = {};
        var jade_interp;

        buf.push("<h1>Hi</h1>");;return buf.join("");
    };

So the in the client side, you can render the html with

    jade['tests/test'](locals);
