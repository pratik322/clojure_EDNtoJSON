(ns json-test
  (:require [clojure.edn :as edn]
            [clojure.data.json :as json]))


(defn load-config
  
  "Given a EDN filename, load & return a JSON FILE"
  
 [filename]
 
(let [edn-read (edn/read-string (slurp filename))
  
      uuid (str (java.util.UUID/randomUUID))
 
      now (new java.util.Date)
      
;;programList Values
      
      programList (assoc (hash-map) :programList (assoc (hash-map) :programId (:name edn-read )
                                                        :version 1
                                                        :createdDtTm "2017-06-16T11:47:25.713-00:00" 
                                                        :updatedDtTm "2017-06-16T11:47:25.713-00:00"
                                                        :name (:display edn-read ) 
                                                        :defaultName (:display edn-read ) 
                                                        :description (:doc edn-read ) 
                                                        :defaultDescription (:doc edn-read )
                                                        :populationId uuid
                                                        :type "REGISTRY"
                                                        :startDtTm "2017-06-16T11:47:25.713-00:00"
                                                        :endDtTm "2017-06-16T11:47:25.713-00:00"
                                                        :priority "NON_CRITICAL"
                                                        :dataType "PATIENT"
                                                        :links (assoc (hash-map) :programLinkId uuid 
                                                                      :createdDtTm "2017-06-16T11:47:25.713-00:00"
                                                                      :updatedDtTm "2017-06-16T11:47:25.713-00:00"
                                                                      :linkURL "/popHealth/synapseRules/app-rules/app-rules-4.11.1.jar"
                                                                      :linkType "RULES")))
      
      ;; Measure Values
      
      measure (:measures edn-read )
      
      
      
      measure-values (reduce (fn [empty-collection measure-map]
                               (conj empty-collection (assoc (hash-map) :programMeasureId uuid 
                                                             :createdDtTm "2017-06-16T11:47:25.713-00:00" 
                                                             :updatedDtTm "2017-06-16T11:47:25.713-00:00" 
                                                             :fullyQualifiedName (name (:name measure-map))
                                                             :name (:display measure-map) :defaultName (:display measure-map)
                                                             :description (:doc measure-map) :defaultDescription (:doc measure-map )
                                                             :measureType "REGISTRY" 
                                                             :measureEntityTypes ["PERSON"] :measureOutcomeType :BOOLEAN
                                                             :sensitive (:sensitive? measure-map) 
                                                             :polarity (clojure.string/replace (:polarity measure-map) #":good" "true")
                                                             :shortDisplay (:short-display measure-map) :defaultShortDisplay (:short-display measure-map))))
                             
                             [] measure)
      
      
      
      
      ;; Aliases Values
      
      concept (:concept-context-relationships edn-read )
      
      aliases (reduce (fn  [empty-collection concept-map]
                        (conj empty-collection (assoc (hash-map) :contextId (:id concept-map)
                                                      :conceptAliases (into [] (:aliases concept-map))
                                                      :contextVersion (:version concept-map))))
                      [] concept)
      
      ;; storing in collection and Generating JSON
      
      json-conversion (json/write-str (assoc programList :measure measure-values :datasets [] 
                                             :ruleNamespaces (:name edn-read)
                                             :ruleFactTypes ["Medication",
                                                             "PersonDemographics",
                                                             "Condition",
                                                             "Result",
                                                             "Claim",
                                                             "Encounter",
                                                             "Procedure"]
                                             :published "true"
                                             :externalId "advocate.copd"
                                             :requiredPrograms [] 
                                             :settings { :customSettings [{:synapseKey "app-rules/program-end-date" :value "2017-06-16T11:47:25.713-00:00"}]}
                                             :programContextToConceptMappings aliases))]
  json-conversion))