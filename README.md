## AWS PEARC 2020 Tutorial


Tutorial content for PEARC 2020 Best Practices on Research Computing.


## Building the Workshop site

The content of the workshops is built using [hugo](https://gohugo.io/). 

To build the content
 * clone this repository
 * [install hugo](https://gohugo.io/getting-started/installing/)
 * The project uses [hugo learn](https://github.com/matcornic/hugo-theme-learn/) template as a git submodule. To update the content, execute the following code
```bash
pushd themes/learn
git submodule init
git submodule update --checkout --recursive
popd
```
 * Run hugo to generate the site, and point your browser to http://localhost:1313
```bash
hugo serve -D
```

## License

This library is licensed under the Amazon Software License.
