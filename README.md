# NAME

Authen::Simple::LDAP - Simple LDAP authentication

# SYNOPSIS

    use Authen::Simple::LDAP;
    
    my $ldap = Authen::Simple::LDAP->new( 
        host    => 'ldap.company.com',
        basedn  => 'ou=People,dc=company,dc=net'
    );
    
    if ( $ldap->authenticate( $username, $password ) ) {
        # successfull authentication
    }
    
    # or as a mod_perl Authen handler
    
    PerlModule Authen::Simple::Apache
    PerlModule Authen::Simple::LDAP

    PerlSetVar AuthenSimpleLDAP_host   "ldap.company.com"
    PerlSetVar AuthenSimpleLDAP_basedn "ou=People,dc=company,dc=net"

    <Location /protected>
      PerlAuthenHandler Authen::Simple::LDAP
      AuthType          Basic
      AuthName          "Protected Area"
      Require           valid-user
    </Location>

# DESCRIPTION

Authenticate against a LDAP service.

# METHODS

- new

    This method takes a hash of parameters. The following options are
    valid:

    - host

        Connection host, can be a hostname, IP number or a URI. Defaults to `localhost`.

            host => ldap.company.com
            host => 10.0.0.1
            host => ldap://ldap.company.com:389
            host => ldaps://ldap.company.com

    - port

        Connection port, default to `389`. May be overriden by host if host is a URI.

            port => 389

    - timeout

        Connection timeout, defaults to 60.

            timeout => 60

    - version 

        The LDAP version to use, defaults to 3.

            version => 3

    - binddn 

        The distinguished name to bind to the server with, defaults to bind
        anonymously.

            binddn => 'uid=proxy,cn=users,dc=company,dc=com'

    - bindpw 

        The credentials to bind with.

            bindpw => 'secret'

    - basedn

        The distinguished name of the search base.

            basedn => 'cn=users,dc=company,dc=com'

    - filter

        LDAP filter to use in search, defaults to `(uid=%s)`.

            filter => '(uid=%s)'

    - scope 

        The search scope, can be `base`, `one` or `sub`, defaults to `sub`.

            filter => 'sub'

    - log

        Any object that supports `debug`, `info`, `error` and `warn`.

            log => Log::Log4perl->get_logger('Authen::Simple::LDAP')

- authenticate( $username, $password )

    Returns true on success and false on failure.

# EXAMPLE USAGE

## Apple Open Directory

    my $ldap = Authen::Simple::LDAP->new(
        host    => 'od.company.com',
        basedn  => 'cn=users,dc=company,dc=com',
        filter  => '(&(objectClass=inetOrgPerson)(objectClass=posixAccount)(uid=%s))'
    );

## Microsoft Active Directory

    my $ldap = Authen::Simple::LDAP->new(
        host    => 'ad.company.com',
        binddn  => 'proxyuser@company.com',
        bindpw  => 'secret',
        basedn  => 'cn=users,dc=company,dc=com',
        filter  => '(&(objectClass=organizationalPerson)(objectClass=user)(sAMAccountName=%s))'
    );

Active Directory by default does not allow anonymous binds. It's recommended
that a proxy user is used that has sufficient rights to search the desired
tree and attributes.

# SEE ALSO

[Authen::Simple::ActiveDirectory](https://metacpan.org/pod/Authen::Simple::ActiveDirectory).

[Authen::Simple](https://metacpan.org/pod/Authen::Simple).

[Net::LDAP](https://metacpan.org/pod/Net::LDAP).

# AUTHOR

Christian Hansen `chansen@cpan.org`

# COPYRIGHT

This program is free software, you can redistribute it and/or modify 
it under the same terms as Perl itself.
