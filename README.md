# NAME

Catmandu::FileStore - Namespace for packages that can make files persistent

# STATUS

[![Build Status](https://travis-ci.org/LibreCat/Catmandu-FileStore.svg?branch=master)](https://travis-ci.org/LibreCat/Catmandu-FileStore)
[![Coverage](https://coveralls.io/repos/LibreCat/Catmandu-FileStore/badge.svg?branch=master)](https://coveralls.io/r/LibreCat/Catmandu-FileStore)

# SYNOPSIS

    # From the command line

    # Export a list of all file containers
    $ catmandu export File::Simple --root t/data to YAML

    # Export a list of all files in container '1234'
    $ catmandu export File::Simple --root t/data --bag 1234 to YAML

    # Add a file to the container '1234'
    $ catmandu stream /tmp/myfile.txt to File::Simple --root t/data --bag 1234 --id myfile.txt

    # Download the file 'myfile.txt' from the container '1234'
    $ catmandu stream File::Simple --root t/data --bag 1234 --id myfile.txt to /tmp/output.txt

    # Delete the file 'myfile.txt' from the container '1234'
    $ catmandu delete File::Simple --root t/data --bag 1234 --id myfile.txt

    # From Perl
    use Catmandu;

    my $store = Catmandu->store('File::Simple' , root => 't/data');

    # List all containers
    $store->index->each(sub {
        my $container = shift;

        print "%s\n" , $container->{_id};
    });

    # Add a new container
    $store->index->add({ _id => '1234' });

    # Get the container
    my $files = $store->index->files('1234');

    # Add a file to the container
    $files->upload(IO::File->new('<foobar.txt'), 'foobar.txt');

    my $file = $files->get('foobar.txt');

    # Stream the contents of a file
    $files->stream(IO::File->new('>foobar.txt'), $file);

    # Delete a file
    $files->delete('foobar.txt');

    # Delete a container
    $store->index->delete('1234');

# DESCRIPTION

Each [Catmandu::FileStore](https://metacpan.org/pod/Catmandu::FileStore) is a [Catmandu::Store](https://metacpan.org/pod/Catmandu::Store) and inherits all its methods,

A [Catmandu::FileStore](https://metacpan.org/pod/Catmandu::FileStore) is package to store and retrieve binary content in
an filesystem, memory or a network. A `Catmandu::FileStore` contains one or more
`Catmandu::FileBag` which is a kind of folder.

Each `Catmandu::FileBag` contains one or more files.

One special `Catmandu::FileBag` is the `index` and contains the listing
of all `Catmandu::FileBag` in the `Catmandu::FileStore`.

# CONFIGURATION

- index\_bag

    The name of the index bag to use when no bag name is give. The index bag is a
    bag containing a listing of all `Catmandu::FileBag`-s in the Store.

        my $index = $store->index;

        $index->each(sub {
            my $bag = shift;

            printf "%s\n" , $bag->{_id};
        });

- index\_class

    The default class implementation to use for an index of `Catmandu::FileBag`-s.
    By default this is the `Catmandu::FileStore` implementation with '::Index' added.

# METHODS

## bag($name)

Create or retieve a bag with name `$name`. Returns a [Catmandu::FileBag](https://metacpan.org/pod/Catmandu::FileBag).

## index

Returns the index  [Catmandu::FileBag](https://metacpan.org/pod/Catmandu::FileBag) for the [Catmandu::FileStore](https://metacpan.org/pod/Catmandu::FileStore).

    my $index = $store->index;

    # Add a new file container
    $index->add({ _id => '1234'});

    # Anf use it...
    my $container = $store->bag('1234');

    $container->upload(IO::File->new('data.txt') , 'data.txt');

## log

Return the current logger. Can be used when creating your own Stores.

E.g.

    package Catmandu::Store::Hash;

    ...

    sub generator {
        my ($self) = @_;

        $self->log->debug("generating record");
        ...
    }

See also: [Catmandu](https://metacpan.org/pod/Catmandu) for activating the logger in your main code.

# AUTHOR

Patrick Hochstenbach, `<patrick.hochstenbach at ugent.be>`

Nicolas Steenlant, `<nicolas.steenlant at ugent.be>`

Nicolas Franck, `<nicolas.franck at ugent.be>`

# SEE ALSO

[Catmandu::Store::File::Simple](https://metacpan.org/pod/Catmandu::Store::File::Simple),
[Catmandu::Store::File::Memory](https://metacpan.org/pod/Catmandu::Store::File::Memory),
[Catmandu::FileBag](https://metacpan.org/pod/Catmandu::FileBag)

# LICENSE AND COPYRIGHT

This program is free software; you can redistribute it and/or modify it
under the terms of either: the GNU General Public License as published
by the Free Software Foundation; or the Artistic License.

See [http://dev.perl.org/licenses/](http://dev.perl.org/licenses/) for more information.
