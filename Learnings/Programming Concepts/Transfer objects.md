
**Purpose:** send data from **Yves** to **Zed** using the `Shared` directories which are shared between **Pyz**

### Why?

- **Data Exchange**: Transfer objects facilitate the ==exchange of data== between Yves and Zed layers. 
- **Structured Communication**: Transfer objects provide a structured way to pass data, enhancing code readability and maintainability. 
- **Intuitive:** We can use pregenerated methods to simplify the process of data transfer

### How?

To define their transfer objects’ schemas, XML is used. Therefore, inside the `src/Pyz/Shared/moduleName/Transfer` directory, add an XML file and call it `module_name.transfer.xml`.

- run `console transfer:generate`

Now a DTO has been generated with specified attributes
### Flow

1. create directory for chosen module in `src/Pyz/Shared`
2. create transfer directory and add an XML file with the naming convention of `module_name.transfer.xml`
3. run `console transfer:generate`
4. you can now use your moduleNameTransfer object throughout your application

### Example

```php
class HelloSprykerTransfer extends AbstractTransfer  
{  
    /**  
     * @var string  
     */    public const ORIGINAL_STRING = 'originalString';  
  
    /**  
     * @var string  
     */    public const REVERSED_STRING = 'reversedString';  
  
    /**  
     * @var string|null  
     */    protected $originalString;  
  
    /**  
     * @var string|null  
     */    protected $reversedString;  
  
    /**  
     * @var array<string, string>  
     */    protected $transferPropertyNameMap = [  
        'original_string' => 'originalString',  
        'originalString' => 'originalString',  
        'OriginalString' => 'originalString',  
        'reversed_string' => 'reversedString',  
        'reversedString' => 'reversedString',  
        'ReversedString' => 'reversedString',  
    ];  
  
    /**  
     * @var array<string, array<string, mixed>>  
     */    protected $transferMetadata = [  
        self::ORIGINAL_STRING => [  
            'type' => 'string',  
            'type_shim' => null,  
            'name_underscore' => 'original_string',  
            'is_collection' => false,  
            'is_transfer' => false,  
            'is_value_object' => false,  
            'rest_request_parameter' => 'no',  
            'is_associative' => false,  
            'is_nullable' => false,  
            'is_strict' => false,  
        ],  
        self::REVERSED_STRING => [  
            'type' => 'string',  
            'type_shim' => null,  
            'name_underscore' => 'reversed_string',  
            'is_collection' => false,  
            'is_transfer' => false,  
            'is_value_object' => false,  
            'rest_request_parameter' => 'no',  
            'is_associative' => false,  
            'is_nullable' => false,  
            'is_strict' => false,  
        ],  
    ];  
  
    /**  
     * @module HelloSpryker  
     *     * @param string|null $originalString  
     *  
     * @return $this  
     */    public function setOriginalString($originalString)  
    {  
        $this->originalString = $originalString;  
        $this->modifiedProperties[self::ORIGINAL_STRING] = true;  
  
        return $this;  
    }  
  
    /**  
     * @module HelloSpryker  
     *     * @return string|null  
     */    public function getOriginalString()  
    {  
        return $this->originalString;  
    }  
  
    /**  
     * @module HelloSpryker  
     *     * @param string|null $originalString  
     *  
     * @throws \Spryker\Shared\Kernel\Transfer\Exception\NullValueException  
     *     * @return $this  
     */    public function setOriginalStringOrFail($originalString)  
    {  
        if ($originalString === null) {  
            $this->throwNullValueException(static::ORIGINAL_STRING);  
        }  
  
        return $this->setOriginalString($originalString);  
    }  
  
    /**  
     * @module HelloSpryker  
     *     * @throws \Spryker\Shared\Kernel\Transfer\Exception\NullValueException  
     *     * @return string  
     */    public function getOriginalStringOrFail()  
    {  
        if ($this->originalString === null) {  
            $this->throwNullValueException(static::ORIGINAL_STRING);  
        }  
  
        return $this->originalString;  
    }  
  
    /**  
     * @module HelloSpryker  
     *     * @throws \Spryker\Shared\Kernel\Transfer\Exception\RequiredTransferPropertyException  
     *     * @return $this  
     */    public function requireOriginalString()  
    {  
        $this->assertPropertyIsSet(self::ORIGINAL_STRING);  
  
        return $this;  
    }  
  
    /**  
     * @module HelloSpryker  
     *     * @param string|null $reversedString  
     *  
     * @return $this  
     */    public function setReversedString($reversedString)  
    {  
        $this->reversedString = $reversedString;  
        $this->modifiedProperties[self::REVERSED_STRING] = true;  
  
        return $this;  
    }  
... 
}
```

Usage in a StringReveser method:

```php
public function reverseString(HelloSprykerTransfer $helloSprykerTransfer)  
{  
    return strrev(  
        $helloSprykerTransfer->getOriginalString(),  
    );  
}
```

And inside the controller:

```php
public function indexAction(Request $request)  
{  
    $string = new HelloSprykerTransfer();  
  
    $string->setOriginalString($request->get('string'));  
    $reversedString = $this->getFacade()
	    ->stringReverser($string);  
  
    return [  
        'string' => $string->getOriginalString(),  
        'reversedString' => $reversedString,  
    ];  
}
```