```php
<?php
function y($m)
{
        echo "pattern is \"$m\"\n";
    $m = preg_replace("/\./", " x ", $m); // transforms '.' into ' x '
    $m = preg_replace("/@/", " y", $m); // transforms '@' into ' y'
    return $m;
}
function x($arg1)
{
    $a = $arg1;
    $a = preg_replace("/(\[x (.*)\])/", "\\2", $a); // transforms "[x <anything>]" into y("<anything>"), then executes it
/*    $a = preg_replace("/\[/", "(", $a); // transforms '[' into '('
    $a = preg_replace("/\]/", ")", $a); // transforms '[' into '('*/
    return $a;
}
$r = x('[x ${exec(echo BITEBITE)}]');
print $r;
?>
```