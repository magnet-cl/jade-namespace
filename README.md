# jade-namespace

A generator of javascript web-friendly files from jade templates

## Usage:

Running the command:
    ./bin/jade-namespace test.jade

Generates a javascript with the compiled javascript function within a
namespace.

For example, if we have a jade file named tests/test.jade containing the following:

    h1 Hi

It becomes a javascript with this content:

    window.jade = window.jade || {};
    window.jade['tests/test'] = function(locals) {
        var buf = [];
        var jade_mixins = {};
        var jade_interp;

        buf.push("<h1>Hi</h1>");;return buf.join("");
    };

Then, in the client side, you can render the html with

    jade['tests/test'](locals);
