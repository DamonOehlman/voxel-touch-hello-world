var async = require('async'),
    AdmZip = require('adm-zip'),
    fstream = require('fstream'),
    mime = require('mime'),
    path = require('path'),
    fs = require('fs'),
    out = require('out'),
    config = require('./config.json'),
    spawn = require('child_process').spawn,
    handlebars = require('handlebars'),
    commandExt = process.platform == 'win32' ? '.bat' : '',
    devToolsPath = path.resolve(config.webworksPath, 'dependencies', 'tools', 'bin'),
    reIgnoreFiles = /^(\.git|node_modules|Jakefile|\.DS_Store|template|output|lib|package.json)/,
    ignoreExtensions = ['.DS_Store', '.bar', '.zip'],
    procOpts = {
        detached: true,
        stdio: 'inherit'
    },
    reIsText = /^(text\/|application\/(json|xml|javascript))/i,
    projectFiles = [];
    
// default the projectname if not set
config.projectName = config.projectName || path.basename(__dirname);

// add the projectname to the app
if (config.app) {
    config.app.projectName = config.app.projectName || config.projectName;
}

// discover the nature of the project through 
task('discovery', { async: true }, function() {
    var reader = fstream.Reader({
            path: __dirname,
            filter: function(entry) {
                var testPath = entry.path.slice(__dirname.length + 1),
                    shouldIgnore = reIgnoreFiles.test(testPath);

                return ! (shouldIgnore || ignoreExtensions.indexOf(path.extname(entry.path)) >= 0);
            }
        });
        
    reader.on('child', function(entry) {
        if (entry.type === 'File') {
            projectFiles.push(entry.path);
        }
    });
    
    reader.on('end', complete);
    out('!{bold}analyzing project structure');
});

task('package', ['discovery'], { async: true }, function() {
    var targetFile = path.join('output', config.projectName + '.zip'),
        basePath = path.resolve(__dirname),
        args = [targetFile],
        relativeFiles, proc;

    relativeFiles = projectFiles.map(function(filename) {
        return filename.slice(basePath.length + 1);
    });

    console.log(args.concat(relativeFiles));
    proc = spawn('zip', args.concat(relativeFiles), {
        cwd: path.resolve(__dirname)
    });

    proc.stdout.on('data', function(buffer) {
        console.log(buffer.toString());
    });
    proc.on('exit', function(code) {
        out('!{bold}zip completed with exit code: ' + code);
        complete();
    });

    out('!{bold}creating archive');
});

task('build', ['package'], { async: true }, function() {
    var args = [
            path.resolve(__dirname, 'output', config.projectName + '.zip'),
            '-o',
            path.resolve('output')
        ];

    if (config.signingPass) {
        args = args.concat(['-g', config.signingPass]);
    }
    else {
        args.push('-d');
    }

    out('!{bold}building bar file');
    spawn(
        path.resolve(config.webworksPath, 'bbwp' + commandExt),
        args, 
        procOpts
    ).on('exit', complete);
});

task('push', { async: true }, function() {
    var args = [
            '-installApp',
            // '-launchApp',
            '-password',
            config.devicePass,
            '-device',
            config.deviceIP,
            path.resolve(__dirname, 'output', 'device', config.projectName + '.bar')
        ],
        proc = spawn(path.resolve(devToolsPath, 'blackberry-deploy' + commandExt), args, procOpts);

    
    out('!{bold}pushing to the device');
    proc.on('exit', complete);
});

task('default', ['build']);