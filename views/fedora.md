#### {% title "Przygotowywanie rpmów dla Fedory 16+" %}

<blockquote>
  {%= image_tag "/images/overrated-ideas.jpg", :alt => "[overrated ideas]" %}
  <p><b>Ideas, in a sense, are overrated.</b>
  Of course, you need good ones, but
  at this point in our supersaturated culture, precious few are so novel
  that nobody else has ever thought of them before.
  <b>It’s really about where you take the idea</b>, and how committed you are
  to solving the endless problems that come up in the execution.</p>
  <p class="author">– Hugo Lindgren,
  <a href="http://www.nytimes.com/2013/01/06/magazine/be-wrong-as-fast-as-you-can.html">Be Wrong as Fast as You Can</a></p>
</blockquote>

…na przykładzie programów MongoDB, CouchDB, ElasticSearch, Redis i PostgreSQL.

Podstawowe informacje o plikach *SPEC*:

* [Working with Spec Files](http://docs.fedoraproject.org/en-US/Fedora_Draft_Documentation/0.1/html/RPM_Guide/ch-specfiles.html)
* [Built-in macros](http://docs.fedoraproject.org/en-US/Fedora_Draft_Documentation/0.1/html/RPM_Guide/ch09s07.html)

Pakiety będziemy budować lokalnie. Składniki rpmów umieszczamy
w katalogach przeszukiwanych przez program *rpmbuild*:

    :::bash
    rpmdev-setuptree

Na koniec, kilka użytecznych poleceń:

    :::bash
    rpm --eval '%configure'
    rpm --eval '%makeinstall'

<!--
Czasami warto wkleić podobną linijkę do pliku SPEC na końcu sekcji *%install*:

    :::bash
    rm -rf $RPM_BUILD_ROOT/usr/include/mongo
-->

## MongoDB

Klonujemy repozytorium z MongoDB:

    :::bash
    git clone git://github.com/mongodb/mongo.git

Przechodzimy do katalogu *mongo* i wykonujemy polecenie:

    :::bash
    cd mongo
    git archive --format=tar --prefix=mongo-2.3.2/ be56edc259 | gzip > ~/rpmbuild/SOURCES/mongo-2.3.2.tar.gz

gdzie `be56edc259` to (ostatni) commit z 6.01.2013.

Plik [mongo-2.3.2.spec](https://raw.github.com/wbzyl/disasters/master/mongod/mongo-2.3.2.spec)
zapisujemy w katalogu `~/rpmbuild/SPECS`.
Przechodzimy do tego katalogu, gdzie wykonujemy polecenia:

    :::bash
    rpmbuild -bi mongo-2.3.2.spec
    rpmbuild -bl mongo-2.3.2.spec

Jeśli powyższe polecenia wykonują się bez blędów, to budujemy pakiet SRC:

    :::bash
    rpmbuild -bs mongo-2.3.2.spec

a następnie pakiet RPM:

    :::bash
    rpmbuild --rebuild mongo-2.3.2-2.fc16.src.rpm

Paczkę instalujemy / uaktualniamy korzystając z programu *yum*:

    :::bash
    cd RPMS/x86_64/
    yum update mongo-2.3.3-2.fc16.x86_64.rpm mongo-server-2.3.2-2.fc16.x86_64.rpm

*Uwaga:* Pakiety RPM powinniśmy budować za pomocą programu *mock*:

    :::bash
    sudo usermod -a -G mock wbzyl # dodajemy siebie do grupy mock
    mock -r fedora-16-x86_64 --resultdir ../RPMS/ mongo-2.3.2-2.fc16.src.rpm

Dlaczego?


## CouchDB

Postępujemy podobnie jak w przypadku MongoDB. Korzystamy
z gotowych łat przygotowanych przez W. Cada:

* [couchdb-rpm](https://github.com/wendall911/couchdb-rpm) (v1.2.1)

Oficjalne repo Fedory (Peter Lemenkov):

* [fedora git](http://pkgs.fedoraproject.org/cgit/couchdb.git/) (v1.2.0)


## ElasticSearch

[Elasticsearch RPMs](https://github.com/tavisto/elasticsearch-rpms) –
an easy way to install elasticsearch on fedora/rhel based systems.


## Redis

TODO.


## PostgreSQL

TODO.